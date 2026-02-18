## Quick introduction
MONSOON3 is another HPC that supporting the PAST2FUTURE projects.

### Official documentation about MONSOON3:
[What is MONSOON](https://code.metoffice.gov.uk/doc/monsoon3/whatIsMonsoon.html)     
[The job scheduler (PBS;Portable Batch System) on monsoon](https://code.metoffice.gov.uk/doc/monsoon3/pbs.html#scheduling-work-with-pbs)    
[MASS and MOOSE](https://code.metoffice.gov.uk/doc/monsoon3/firstHourOnMass.html#your-first-hour-on-mass)    
[use quota to know your amount limitation](https://code.metoffice.gov.uk/doc/monsoon3/file_systems.html#filesystems-datadir-and-quotas)

## start-point GC5 suites on MONSOON3
There is a new suites u-dv308 (GC5-central N96 ORCA1 UM13.8 piControl), which is hosted by EXC and EXD. Here we make a copy of u-dv308 (u-dv344), then revise its rose configuration with the configuration of **u-du021***(MONSOON3 coupling suite) and **u-do322**(ARCGER2 GC5 suite) as a reference.     
Then we take the revised u-dv344 (GC5-central N96 ORCA1 UM13.8 piControl on monsoon3) as a start-point.
Note that there is some difference in the configuration of `instal ancil` task between GC3 and GC5.    

### The configuration of GC5
Note that there is some difference in the configuration of `instal ancil` task between GC3 and GC5.    
The NEMO and SI output variables can be changed through 



### Debug during the setup of u-dv344
#### Mismatch of calendar and outputstream
The default calendar for u-dv344 is **gregorian**, it is different with that for the GC3 Eocene and LGM suites.
For the gregorian calendar, all the time unit for reinitialisation of the outputstreams (reinit_unit) should be `Real Month(4)`. Or the outputstream with other units can not be generated.
Then you may get a error in the <your work directory>/cylc-run/u-dv344/runN/log/job/25001201T0000Z/coupled/NN/job.err as below:
```
????????????????????????????????????????????????????????????????????????????????
???!!!???!!!???!!!???!!!???!!!       ERROR        ???!!!???!!!???!!!???!!!???!!!
?  Error code: 1
?  Error from routine: io:file_open
?  Error message: Failed to open file
?  Error from processor: 0
?  Error number: 17
????????????????????????????????????????????????????????????????????????????????
```
#### ***mismatch between file_def_nemo_ice.xml and field_def_nemo_ice.xml***
 <your work directory>/cylc-run/u-dv344/runN/log/job/25001201T0000Z/coupled/NN/job.err as below:
```
> Error [CField::solveGridReference(void)] : In file '/home/users/harry.shepherd/cylc-run/infrastructure_suite_deploy_login.exa.sc/share/xios/src/node/field.cpp', line 1302 -> A grid must be defined for field 'sisnconc' .

MPICH ERROR [Rank 616] [job id e8932228-88a0-4a1c-8873-793d37b63163] [Fri Dec 12 23:04:41 2025] [nidd1411] - Abort(-1) (rank 616 in comm 0): application called MPI_Abort(MPI_COMM_WORLD, -1) - process 616
```

The `file_def_nemo_ice.xml` determines the list and frequencies of the variables to be outputed. The variables claimed in the `file_def_nemo_ice.xml` must be defined in the `field_def_nemo-oce.xml`, which is linked from `$CYLC_SUITE_SHARE_DIR/fcm_make_ocean/build-ocean/etc/field_def_nemo-oce.xml` as documented in the `./app/si3/rose-app.conf`.    
In our suite, we resolve this bug by replace our `domain_def_ice.xml` with that in u-do322, a GC5 suite owned by Xu. In the future, if the internal suite is fully ported to MONSOON3, we may not get this.

#### ***no sofwficb in NEMO ancil***   

 <your work directory>/cylc-run/u-dv344/runN/log/job/25001201T0000Z/coupled/NN/job.log as below:
 ```
          ===========

 iom_varid, file: ./runoff_1m_nomask.nc, var: sofwficb not found


  ===>>> : E R R O R

          ===========

 STOP
 No iceberg runoff data read in for Greenland. Check input file or set rn_greenland_calving_fraction=0.0


 huge E-R-R-O-R : immediate stop
```
This bug should be attributed to the Incomplete runoff ancillary file (/common/share/monsoon_ancils_ocean/hadgem3/forcing/ocean/eORCA1v2.2x/), which dont't have the file eORCA1_runoff_GO6_icb.nc which is needed here.     
eORCA1_runoff_GO6_icb.nc can be found on archer2 (/work/y07/shared/umshared/hadgem3/forcing/ocean/eORCA1v2.2x), so we port it from archer2 to monsoon3. **report the imcomplete file to the ncas people**

#### Explosion of the NEMO sea suraface height and sailinity
 <your work directory>/cylc-run/u-dv344/runN/log/job/25001201T0000Z/coupled/NN/job.log as below:
```
  ===>>> : E R R O R

          ===========


     ==>>>   nemo_gcm: a total of  1  errors have been found

             Look for "E R R O R" messages in all existing ocean_output* files

                     iom_close ~~~ close file: ./geothermal_heating.nc ok
                     iom_close ~~~ close file: ./runoff_1m_nomask.nc ok
                     iom_close ~~~ close file: ./runoff_1m_nomask.nc ok
[INFO] Nemo output file solver.stat not avaliable
[INFO] Ocean output from file run.stat
 it :       1    |ssh|_max:  0.2121747942899510E+01 |U|_max:  0.1916948210547619E+01 S_min:  0.3686317946397103E+00 S_max:  0.4341922647751776E+02
 it :       2    |ssh|_max:  0.2122421174448312E+01 |U|_max:  0.1918937899674541E+01 S_min:  0.3686122685981999E+00 S_max:  0.4341970512130442E+02
 it :       3    |ssh|_max:  0.2122247217306716E+01 |U|_max:  0.1918040254011808E+01 S_min:  0.3686296032653921E+00 S_max:  0.4342016674559835E+02
 it :       4    |ssh|_max:                     NaN |U|_max:  0.0000000000000000E+00 S_min:  0.1797693134862316+309 S_max: -0.1797693134862316+309
```
if you wanna check the output.abort_*.nc you may need to rebuild it. by the script `rebuild_nemo`. On MONSOON3 it is located at `/data/users/moci.mon/bin/REBUILD_NEMO/nemo_br_r10277_cpe2305_cce15`. For convenience, we copy it to the ~/bin/rebuild_nemo_zikun. A example for it: `rebuild_nemo_zikun output.abort 108`.

This error also shows in WORK/coupled/ocean.output, which is the log of NEMO. However, in this case this explosion seems to stem from the CFL question in the UM. It disappear after we uplift the ATMOS_TIME_STEPS_PER_DAY from 48 to 72.   

**note that** after increase ATMOS_TIME_STEPS_PER_DAY from 72 to 96 this error appear again. That may be caused by the influence of the restart file. The original ATMOS_TIME_STEPS_PER_DAY of u-do332, from which the restart file of our suite is taken from, is 72.


#### Explosion of the atmospheric component (high-level wind, potential temperature, )
There are many [Known UM Failue points](https://code.metoffice.gov.uk/trac/um/wiki/KnownUMFailurePoints). I nearly encountered all of them in the GC5-central setup on the MONSOON3 for only one reason. That means these error information does't mean much to our debugging. I made many attemption to fix it, like uplifting the high-level wind damping and change the UKCA scaling coefficient, but none of them work. The model broke down after 4 months' running with different error information anyway.

Finally, I suspect it may comes from the inconsistence bettween the ancillery files. Therefore, I compared the ancils list with one existing GC5-emergent suite (u-dv935). I found the `UN_ANCIL_DIR` in the 'app/instal_ancil/rose_app.conf' is `$UMDIR/ancil/atmos/GC5` in my suite, but `$UM_INSTALL_DIR/atmos/GC5` in `u-dv935`. So I modify it as in the 'u-dv935', and it finally got through the timepoint!

Therefore, I suggest that when a model breaks down after several months of simulation and shows abnormal short-term tendencies in certain physical variables, the configuration of the ancillary datasets should be carefully examined.

#### Errors happened in Ozone scheme

##### My investigation on the ozone scheme
The Ozone scheme is work with a interaction between the UM and the Ozone tasks.     
- On the ozone tasks (retrieve_ozone, redistribute_ozone):
On January of each year, The `retrieve_ozone` will setup the files (orogrophy, DENSITY*R*R AFTER TIMESTEP 00253, the tropopause_altitude 30453) needed by the `ozone_distribution`. A successsful `retrieve_ozone` demands the ancillary files of the past one or two years. The `retrieve_ozone` will proceed a python script (`retrieve_ozone_data.py`), which is claimed to be downloaded from FCM in the rose-suite.conf. For the start time stamp (must be {start_year}0101), the `retrieve_ozone_data.py` will not require the 'redistribute_ozone'.    

In the `redistribute_ozone` task, The python script `src/contrib/redistribute_ozone.py ` downloaded from FCM will be used to calculate the ozone_distribution of the nextyear. Note that the python scripy is built by the task `contrib_package_build`.    
- On the UM
There are two ouput stream (pz, po). Note that, the content of pz is set by the switch `suite conf > Ozone Restribution > Redistribuition ozone`. In the GUI of `Rose config-edit`, the stash request of pz will show to be empty. But after installation of suite, the UM Optional key (ozone) will add two variables (DENSITY*R*R AFTER TIMESTEP 00253, the tropopause_altitude 30453) in it. In the `postproc` task (function do_ozone in the script `share/fcm_make_pp/build/bin/atmos.py`), the two variables will be extracted to the `po` outputstream. And `po` will be the source outputstream to generate the OZONE ancil for the next year.




#####  On the first time stamp, Required data since NRun not found on disk: Year: 2501 Months: 1,2,3,4,5,6,7,8,9,10,11,12

For Ozone scheme, the basis timestamp must be the {start_year}0101, so that the ozone initialization can proceed correctly.


#####  On the second year, Required data since NRun not found on disk: Year: 2501 Months: 1,2,3,5,6,8,9,11  

On the first month of the second year, the outputs of `po` outputstream is incomplete with a few monthes lacked.

My attemption:     
1. find the source of the error information: `./share/fcm_make_pp/build/bin/atmos.py`
```
602             icode = transform.extract_to_pp(
603                 [os.path.join(self.share, s) for s in source_files],
604                 fields, output_stream, data_freq='1m'
605                 )
606             if icode == 0:
607                 utils.log_msg('Successfully extracted ozone fields')
608             else:
609                 # Fail immediately - we don't want source file(s)
610                 # subsequently processed and archived
611                 utils.log_msg('Failed extracting ozone fields',
612                               level='FAIL')
613
614             output_files = housekeeping.get_marked_files(
615                 self.share, self.ff_match(output_stream), ''
616             )
```
2. Learn more about the method `transform.extract_to_pp()` which is a function in the `./share/fcm_make_pp/build/bin/atmos_transform.py`
```
266     outfile = prefix + outstream + current_year + '.pp'
267     tmpfile = None
268     if len(sources) < 1:
269         utils.log_msg('No source files found for field extraction\n\t',
270                       level='WARN')
271     elif MULE_AVAIL:
272         tmpfile = _extract_to_pp_mule(sources, fields, outfile, data_freq)
273     elif IRIS_AVAIL:
274         tmpfile = _extract_to_pp_iris(sources, fields, outfile, data_freq)
275     else:
276         utils.log_msg(
277             'Either Mule or IRIS required to extract fields to PP format',
278             level='WARN'

```
As we can see there are two option for extract, and MULE is the prior one. If we lift the check of IRIS_AVAIL ahead of MULE. All the variables will be correctly extracted.


## From piControl to Eocene on MONSOON3    
We copy the GC5-central piControl suite u-dv344 as a new suite u-dv769.    
There are three parts of parameters demand being changed:
- {path to your suite}/app/install_ancil/rose-app.conf
- 
### ancil_list
Based on the ancil_version scripts of Seb, I try to setup the Eocene ancil_files in u-dv769. However, there is some significant differences in NEMO ancil set bettween the GC3 and GC5 configurations: 
#### NEMO
##### Bathmetry and domain_CFG (configuration change)
In GC3 the bathmetry is controlled by the ancil_file `BATHY_METER`, while in GC5 it is included in the `domain_cfg.nc`. We may need to build a Eocene `domain_cfg.nc` based on the bathymetry file, so we can make it consistency.   
I am trying to generate it with the `DOMAINcfg` tools.    
Some repository for reference：
[A example for regional model](https://github.com/NOC-MSM/Regional-NEMO-Medusa)
See its [Wiki](https://github.com/NOC-MSM/Regional-NEMO-Medusa/wiki/) for usage.
[Official resource](https://forge.nemo-ocean.eu/nemo/nemo.git)

My attemption:
1. [On Archer2] git clone https://forge.nemo-ocean.eu/nemo/nemo.git ./nemo-main
2. cd nemo-main
3. load the demanded modules:
```
                 module load cray-hdf5-parallel
                 module load cray-netcdf-hdf5parallel
```
4. cd tools; ./maketools -m X86_ARCHER2-Cray -n DOMAINcfg
5. vim `namelist_cfg`, use the parameters as below:
```
!-----------------------------------------------------------------------
&namcfg        !   parameters of the configuration
!-----------------------------------------------------------------------
   !
   ln_e3_dep   = .true.    ! =T : e3=dk[depth] in discret sens.
   !                       !      ===>>> will become the only possibility in v4.0
   !                       ! =F : e3 analytical derivative of depth function
   !                       !      only there for backward compatibility test with v3.6
      !                      ! if ln_e3_dep = T
      ln_dept_mid = .false.  ! =T : set T points in the middle of cells
   !                       !
   cp_cfg      =  "orca1"  !  name of the configuration
   jp_cfg      =       1   !  resolution of the configuration
   jpidta      =     362   !  1st lateral dimension ( >= jpi )
   jpjdta      =     332   !  2nd    "         "    ( >= jpj )
   jpkdta      =      75   !  number of levels      ( >= jpk )
   Ni0glo      =     362   !  1st dimension of global domain --> i =jpidta
   Nj0glo      =     332   !  2nd    -                  -    --> j  =jpjdta
   jpkglo      =      75
   jperio      =       4   !  lateral cond. type (between 0 and 6)
   ln_domclo = .true.      ! computation of closed sea masks (see namclo)
```
6. `sbatch` the `sub` script shown as below:
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
7. You got the domain_cfg.nc and mesh_mask.

##### viscosity coefficient (configuration change)
In GC3 the viscosity coefficient in tropical area is modified by the `AHMCOEF` file, while in GC5 it is turned into a 3d distribution documented in `eddy_viscosity_3D.nc`. Therefore a new script should be build to generate the 3D distribution of viscosity which is adapted to Eocene mask.    
In the GC3 version, the viscosity file is generated by the script of Chalie (which is documented in the idiot's guidance). Refer to `/home/users/cwilliams2011/analysis.d/projects.d/sweet.d/gc31n96orca1_eo.d/build.d/ocean.d/ahmcoef.py.` on JASMIN for further detail.    
##### runoff or river (new ancil file)
In the GC5 model, except for the `runoff_1m_nomask.nc`, there is another file named `river_number_nemo.nc` required. I am not sure whether it is neccessary to make change on it.


#### UM
##### Chlorophyll qrclim.sea (potential element)
This is the same for piControl and eocene in the GC3 suite. In GC5, it is updated. Not sure whether it will significantly impact the results.
##### easyareosol (potential element)
There is some ancil seems to be fixed impcat on radiation of the areosol. Will it influence the model? Where to set it?
##### river_number_um (new ancil file)
For UM, there are two ancils requreid: `qrparm.rivseq` and `river_number_um`. The Eocene `qrparm.rivseq` have been applied in GC3, but no `river_number_um` yet.
##### qrclim.rivstor riverstorage (new ancil file)
Only for GC5. Switch it off in the configuration?
##### Ozone (potential element)
The dealing of ozone is different in the set of Charlie and Seb. In the Charlie's configuraation it is taken from UKESM1 4xCO2 monthly climatology (85 levels), and zonally palaeotise at every month/level. In contrast, in the Seb's suite, it is calculated by interactive ozone scheme.     
Note that, the ozone scheme is not correctly set in our GC5 suite. Here we simply set it as a PI zonal-mean field. In the future, examine the ozone output to check whether it is used (07/01/2026).    
##### Soil roughness (new ancil file)
There is one new ancil file in GC5 named soil_roughness
##### orog_radiation_parameters, orog_radiation_parameter, qrparm.orog_wavedrag, qrparm.ash (new ancil files)
Some GC5 ancils relevant to the paramterization of the orography. None of them are supplied in GC3. Swich them off?
##### qrclim.aerosols${LEVELS}.nc (new ancil files)
These ancils seem to document fixed areosols by different vertical levels. We may have need to switch the relevant parameterizations off.
##### hydrosheds qrparm.hydtop (new ancil files)
This ancil file domucment the Mean Topographic Index and STANDARD DEVN IN TOPOGRAPHIC INDEX, which influence the average wetness tendency of a grid cell and the sub-grid heterogeneity of soil moisture, respectively. They may be used in the JULES.

### Switch off some scheme based on information about present-day bathymetry (Copy from [Idiot's guide to setting up GC3.1 Eocene suite on NEXCS v3.docx](https://uob-my.sharepoint.com/:w:/g/personal/wb19586_bristol_ac_uk/EcIwjv71mMFDgCKJqzcjB_oBGmRss2O40IxQPvVG6BF4xw?e=vgAmjN)):
- Ocean ancils used by the standard model are listed below - first 4 are based on information about present-day bathymetry which can’t be replicated for the Eocene, so just switch off spatially varying parts of these schemes:
    - Tidal mixing (M2, K1 files): Switch it off tidal mixing scheme  (i.e. remove key_zdftmx in ~/roses/<SUITEID>/app/fcm_make_ocean/rose-app.conf)
    - Geothermal heating: Switch it off (i.e. set ln_trabbc= .false. in /home/d05/<USERNAME>/roses/<SUITEID>/app/nemo_cice/rose-app.conf)
    - Indonesian throughflow mixing: Switch it off (i.e. set ln_tmx_itf=.false. in /home/d05/<USERNAME>/roses/<SUITEID>/app/nemo_cice/rose-app.conf)
    - 2D bottom friction (bfr_coef.nc): Switch it off (i.e. set ln_bfr2d=.false. in /home/d05/<USERNAME>/roses/<SUITEID>/app/nemo_cice/rose-app.conf)
    - Viscosity (ahmcoef.nc) - need to make new file, but using Eocene mask.  Purpose of this file is to reduce viscosity between 20° N/S to increase equatorial current speed and vertical shear.  This can't be applied next to western boundaries for stability reasons, or to ensure the WBCs remained sensible.  So it needs to be 0 between +/- 20°, except for a 10° buffer to the east of any land mass, followed by a 5° ramp from 100 to 0.  To do this, need to take modern version (at /projects/ocean/hadgem3/ancil/ocean/eORCA1v2.2x) then, using this, create new version using code on JASMIN at /home/users/cwilliams2011/analysis.d/projects.d/sweet.d/gc31n96orca1_eo.d/build.d/ocean.d/ahmcoef.py.  Final version needs to be transferred to NEXCS, e.g. at /home/d05/cwilliams/gc31/final_ancils_round2/ocean
    - Iceberg calving & ice sheet basal melt flux need to be switched off (as no ice sheets).  To do this, set in /home/d05/<USERNAME>/roses/<SUITEID>/app/
nemo_cice/rose-app.conf:
        -	ln_icebergs=.false.
        -	nn_isf=0
        -	rn_antarctica_total_fw_flux=0
        -	rn_greenland_total_fw_flux=0
Note: Last two may not be necessary as first two should switch off fluxes and hence their magnitude is irrelevant, but just in case…
    - Postprocessing/archiving may fall over if it tries to archive iceberg files but none exist (because iceberg advection scheme is off), so set in /home/d05/<USERNAME>/roses/<SUITEID>/app/postproc/rose-app.conf:
        - archive_iceberg_trajectory=true
        - nemo_icebergs_rst=true



## GCOM
```
fcm co fcm:gcom.xm_br/dev/andymalcolm/vn7.5_meto_ex1a_configs ./vn7.5
fcm co fcm:gcom.x_tr $HEAD gcom_tr
```
combine the two GCOM repositories, and run the modified suite on the cazccylc1 node.
The guidance to instal GCOM [LINK](https://code.metoffice.gov.uk/trac/gcom/wiki/GCOMinstall_MetOffice?revision=11)

## MOOSE    
**know more about moose**: echo "moo help" | qsub -q collabmass -j oe    
**Know your project**: echo "moo projinfo --members --long project-ukesm" | qsub -q collabmass -j oe     
### DEBUG
#### (TSSC_CONFLICT_WITH_EARLIER_COMMAND) command conflicts with another command.
```
[WARN]  moo.py: Moose Error: user-error (see Moose docs). (ReturnCode=2) File: /home/users/zikun.ren.ext/cylc-run/u-dv344/run18/share/data/History_Data/dv344a.da25020101_00
[WARN]  [SUBPROCESS]: Command: moo put -f -vv /home/users/zikun.ren.ext/cylc-run/u-dv344/run18/share/data/History_Data/dv344a.da25030101_00 moose:crum/u-dv344/ada.file
[SUBPROCESS]: Error = 2:
        put command-id=2091026403 failed: (SSC_TASK_REJECTION) one or more tasks are rejected.
  /home/users/zikun.ren.ext/cylc-run/u-dv344/run18/share/data/History_Data/dv344a.da25030101_00 -> moose:/crum/u-dv344/ada.file/dv344a.da25030101_00: (TSSC_CONFLICT_WITH_EARLIER_COMMAND) command conflicts with another command.
  Conflicting command-ids: 2091023245,
put: failed (2)
```
This error happens when the outrage of MONSOON3 happens during the archiving.
  
use `echo "moo si" | qsub -q collabmass -j oe` to check your transfer jobs.     
If there is any running but blocked jobs, use `echo "moo kill <command-id>" | qsub -q collabmass -j oe` to kill them.
`
