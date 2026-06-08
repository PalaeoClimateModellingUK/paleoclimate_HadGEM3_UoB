# prerequisites
Before starting the procedures below, you need to have one ORCA1 bathymetry. Please refer to [Gabriel Pontes's notes](https://github.com/pontesgm4/HadGEM3-GC5_palaeo/blob/main/step1_make_ocean/step1_make_domain_cfg.md) for the procedures to generate the bathymetry.


# First step: generate mesh_mask.nc from bathymetry using the DOMAINcfg of NEMO4.0
Caveat: The workflow described here is based on NEMO4, whereas GC3.1 uses NEMO3.6 as its ocean component. Although the generated mesh_mask.nc is compatible with the subsequent step, the difference in model versions should be noted. Minor differences in results may exist.
## 1. Check out the NEMO utils
Please run the following commands on archer2
```
mkdir <your working directory>
cd <your working directory>
fcm co https://code.metoffice.gov.uk/svn/nemo/utils utils
cd util
```
## 2. update the archived environment set for ARCHER2
`cp build/arch/NOC/arch-X86_ARCHER2-Cray.fcm build/arch/NOC/arch-X86_ARCHER2-Cray-updated.fcm`
make changes as below:
```
--- arch-X86_ARCHER2-Cray.fcm   2026-02-03 11:49:57.000000000 +0000
+++ arch-X86_ARCHER2-Cray-updated.fcm   2026-05-12 13:39:30.000000000 +0100
@@ -32,7 +32,7 @@
 #
 %NCDF_HOME           $NETCDF_DIR
 %HDF5_HOME           $HDF5_DIR
-%XIOS_HOME           /work/n01/shared/acc/xios-trunk
+%XIOS_HOME           /work/n01/shared/nemo/xios-trunk
 #OASIS_HOME

 %NCDF_INC            -I%NCDF_HOME/include -I%HDF5_HOME/include
```
## 3. load the demanded modules and compile the DOMAINcfg
```
              module use /work/y07/shared/umshared/moci/modules/modules
              module load GC5-PrgEnv
              cd tools
              ./maketools -m X86_ARCHER2-Cray-updated -n DOMAINcfg
```
## 4. Edit `namelist_cfg`
`vim namelist_cfg` and use the setting as below, with <your ORCA1 bathymetry file> changed to your bathmetry file:
```
!!>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
!! NEMO/OPA  Configuration namelist : used to overwrite defaults values defined in SHARED/namelist_ref
!!>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
!
!-----------------------------------------------------------------------
&namrun        !   parameters of the run
!-----------------------------------------------------------------------
   cn_exp      =  "domaincfg"  !  experience name
   nn_it000    =       1   !  first time step
   nn_itend    =      75   !  last  time step (std 5475)
/
!-----------------------------------------------------------------------
&namdom        !   space and time domain (bathymetry, mesh, timestep)
!-----------------------------------------------------------------------
   cn_fcoord   =  '/work/y07/shared/umshared/hadgem3/grids/ocean/eORCA1v2.2x/eORCA1_coordinates_nc4.nc_from_MR'
   cn_topo     =  <your ORCA1 bathymetry file>
   cn_bath     =  'Bathymetry'
   jphgr_msh=0
   ldbletanh=.true.
   ppa0=103.9530096000000
   ppa1=2.415951269000000
   ppa2=100.7609285000000
   ppacr=7.0
   ppacr2=13.0
   ppdzmin=999999.0
   pphmax=999999.0
   ppkth=15.35101370000000
   ppkth2=48.02989372000000
   ppsur=-3958.951371276829
   rn_atfp=0.1
   rn_e3zps_min=25.0
   rn_e3zps_rat=0.2
   rn_hmin=-8.0
   rn_rdt=1350.0
   nn_msh      =    1      !  create (=1) a mesh file or not (=0)
/
!-----------------------------------------------------------------------
&namcfg        !   parameters of the configuration
!-----------------------------------------------------------------------
   !
   ln_e3_dep   = .true.    ! =T : e3=dk[depth] in discret sens.
   !                       !      ===>>> will become the only possibility in v4.0
   !                       ! =F : e3 analytical derivative of depth function
   !                       !      only there for backward compatibility test with v3.6
   !                       !
   !cp_cfg='orca'
   !jp_cfg=1
   !jperio=6
   !jpidta=362
   !jpiglo=362
   !jpjdta=332
   !jpjglo=332
   !jpkdta=75
   !jpizoom  =  1
   !jpjzoom  =  1
   !ln_use_jattr  =  .false.
   !ln_domclo=.false.
   cp_cfg      =  "orca"   !  name of the configuration
   jp_cfg      =       1   !  resolution of the configuration
   jpidta      =     362   !  1st lateral dimension ( >= jpi )
   jpjdta      =     332   !  2nd    "         "    ( >= jpj )
   jpkdta      =      75   !  number of levels      ( >= jpk )
   Ni0glo      =     362   !  1st dimension of global domain --> i =jpidta
   Nj0glo      =     332   !  2nd    -                  -    --> j  =jpjdta
   jpkglo      =      75
   jperio      =       6   !  lateral cond. type (between 0 and 6)
   ln_use_jattr = .false.  !  use (T) the file attribute: open_ocean_jstart, if present
                           !  in netcdf input files, as the start j-row for reading
   ln_domclo = .false.     ! computation of closed sea masks (see namclo)
/
!-----------------------------------------------------------------------
&namzgr        !   vertical coordinate
!-----------------------------------------------------------------------
   ln_zps      = .true.    !  z-coordinate - partial steps
/
!-----------------------------------------------------------------------
&namlbc        !   lateral momentum boundary condition
!-----------------------------------------------------------------------
/
!-----------------------------------------------------------------------
&nammpp        !   Massively Parallel Processing                        ("key_mpp_mpi)
!-----------------------------------------------------------------------
/
```
## 5. Submit make_domain_cfg.exe
```bash
vim submit_domaincfg.sh
```
Paste:
```bash
#!/bin/bash -l
#
#SBATCH --job-name=DOMAINcfg_test
#SBATCH --output=logs/DOMAINcfg_%j.out
#SBATCH --error=logs/DOMAINcfg_%j.err
#SBATCH --time=60:00
#SBATCH --chdir=<your directory>
#SBATCH --partition=serial
#SBATCH --qos=serial
#SBATCH --account=n02-P2F
#SBATCH --exclusive=mcs
#SBATCH --ntasks=1
#SBATCH --mem=100G
#SBATCH --export=NONE

srun -n 1 ./make_domain_cfg.exe
```
change <your directory> to your target directory.
## 6. `cd DOMAINcfg`; mkdir logs ; `sbatch` the `sub` script shown as below:
```
#!/bin/bash -l
#
#SBATCH --job-name=DOMAINcfg_test
#SBATCH --output=logs/DOMAINcfg_%j.out
#SBATCH --error=logs/DOMAINcfg_%j.err
#SBATCH --time=60:00
#SBATCH --chdir=/work/n02/n02/an25872/NEMO_SRC/nemo-main/tools/DOMAINcfg
#SBATCH --partition=serial
#SBATCH --qos=serial
#SBATCH --account=n02-P2F
#SBATCH --exclusive=mcs
#SBATCH --ntasks=1
#SBATCH --mem=100G
#SBATCH --export=NONE

srun -n 1 ./make_domain_cfg.exe
```
create log directory:     
`mkdir -p logs`
```bash
sbatch submit_domaincfg.sh
```
## 7. Verify output
Successful completion should generate:

- `domain_cfg.nc`
- `mesh_mask.nc`

Inspect with:

```bash
module load ncview
ncview domain_cfg.nc
```

Check that:

- `bathy_metry` matches the expected Pliocene reconstruction
- land–sea mask is correct
- key gateways are open/closed as intended
- no obvious artefacts remain



