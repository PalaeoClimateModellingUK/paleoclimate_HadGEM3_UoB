In step2.2 we have already built a Eocene suite on MONSOON3 (u-dv769). However, for the resource limitation on MONSOON3, the simulation runs very slow.     
Therefore, we plan to test the suites on ARCHER2 instead. This note documented our effort to port the u-dv769 to ARCHER2 and some further modification on u-ea539 (the archer2 version of u-dv769).

## setting archer2_python_env
The biggest challenge is the setting up of the python_env, which is used for the interactive ozone, on the ARCHER2. Different with MONSOON3, on archer2 there is no existing module like scitools or um_tool can be directly used in python_env (cray-python helps a lot, but it don't have iris). To make the ozoen scheme runnable on ARCHER2. I set up a python_env as below:
```
  1 #!/bin/bash -l
  2 #
  3 # Usage python_env CMD_WITHOPTS
  4 #
  5 # ENVIRONMENT
  6 #   PYTHON_ENVIRONMENT_PATH   The version of the software stack you want to run
  7 set -eu
  8
  9 module_count(){
 10     module -t list 2>&1 | wc -l
 11 }
 12
 13
 14 module_load(){
 15     PRE_LOAD_COUNT=$(module_count)
 16
 17     module load "${1}"
 18     # Check module count to determine whether module load was successful.
 19     if (( ${PRE_LOAD_COUNT} == $(module_count) )); then
 20         echo "[ERROR] Failed to load: ${1}"
 21         exit 1
 22     fi
 23 }
 24
 25 export OMP_NUM_THREADS=1
 26 export OPENBLAS_NUM_THREADS=1
 27 export MKL_NUM_THREADS=1
 28 export VECLIB_MAXIMUM_THREADS=1
 29 export NUMEXPR_NUM_THREADS=1
 30 export PYTHONPATH=/work/y07/shared/umshared/lib/python3.9:/work/y07/shared/utils/core/bolt/0.8/modules:$PYTHONPATH
 31
 32
 33
 34 source /work/n02/n02/an25872/myvenv/bin/activate
 35
 36 exec "$@"

```
Among this setup, the python module `/work/y07/shared/umshared/lib/python3.9` is copied from cray-python.    
And the venv is created according to the [offcial guidance of ARCHER2](https://docs.archer2.ac.uk/user-guide/python/). And I installed the other python package such as: iris, pyproj... under this venv.      

With these setup, we can go through the task `ants_package_build` and `contrib_package_build`. However the `redistrbute_ozone` fail for the missing of `mo_pack`, which is a package not provided by pypi.     

Fortunately, following the offical GC5 suite `u-dk922`, I found there is a miniconda enviroment under the /home directory (/home/n02/n02/fcm/miniconda3/envs/ants_env/). And the python_env of u-dk922 is as below:
```
  1 #!/bin/bash -l
  2 #
  3 # Usage python_env CMD_WITHOPTS
  4 #
  5 # ENVIRONMENT
  6 #   PYTHON_ENVIRONMENT_PATH   The version of the software stack you want to run
  7
  8 set -eu
  9
 10 source ~fcm/ants_env.sh
 11
 12 exec "$@"
```
For unknow reason, this `python_env` still don't work for our suite. But the 'mo_pack' is contained in this environment. Therefore, we copy the `/home/n02/n02/fcm/miniconda3/envs/ants_env/lib/python3.10/site-packages/mo_pack*` into my venv lib `/work/n02/n02/an25872/myvenv/lib/python3.10/`, and all the shared library files lib*.so* under `/home/n02/n02/fcm/miniconda3/envs/ants_env/lib/` into `/work/n02/n02/an25872/myvenv/lib/`.     

With these setup, the `ozone_reistribute` finally works successfully.

## Change the viscosity coefficients
In u-dv769, the viscosity coefficients is [tentatively set as constant](https://github.com/PalaeoClimateModellingUK/paleoclimate_HadGEM3_UoB/blob/main/Paleo_suites_setup/step2.2_Eocene_setup_on_MONSOON3%20(notes%20for%20u-dv769).md#211b-viscosity-coefficient-configuration-change) for the lack of `eddy_viscosity_3D.nc`.     

Now, Gabriel have build up a workflow to generate the `eddy_viscosity_3D.nc` from the bathymetry file. Therefore, here we can set the `nn_ahm_ijk_t` at `nemo > namelist > Dynamics options (namdyn) > Lateral diffusion (namdyn_ldf)` back to `Read in from eddy_viscosity_3D.nc`.     

The workflow to generate `eddy_viscosity_3D.nc` is over [here](https://github.com/pontesgm4/HadGEM3-GC5_palaeo/blob/main/step1_make_ocean/step2_make_eddy_viscosity.md#generating-the-eddy_viscosity_3dnc-file).

## Ozone scheme update
The definition of tropopause demand being updated under extremely hot climate like Eocene.      
Add `/home/n02/n02/an25872/FCM/UM/vn13.8_Seb_tropopause` to the `um_sources` under the `env` section under fcm_make_um.      
The changes of this version is as listed below      
```
  1 --- vn13.8_Seb_tropopause/src/atmosphere/radiation_control/tropin.F90   2026-08-01 14:21:01.115905000 +0100
  2 +++ vn13.8/src/atmosphere/radiation_control/tropin.F90  2026-08-01 14:15:43.775968000 +0100
  3 @@ -115,10 +115,10 @@
  4  ! ---------------------------------------------------------------------
  5  ! Define local variables:----------------------------------------------
  6
  7 -INTEGER :: i, j, k, j0f, j1f, z,                                                  &
  8 +INTEGER :: i, j, k, j0f, j1f,                                                  &
  9                                      ! Loopers over level & point
 10 -!kun!     kp1,                                                                      &
 11 -!kun!     !  K+1, except where this would cause out-of-bounds reference
 12 +     kp1,                                                                      &
 13 +!     !  K+1, except where this would cause out-of-bounds reference
 14             nneigh,                                                             &
 15                              ! Number of well-defined tropopauses among
 16  !     ! the 8 nearest neighbours of a point without one of its own
 17 @@ -138,8 +138,7 @@
 18                        ! in SCUM code.
 19
 20
 21 -REAL(KIND=real_umphys) :: cp_over_g, p_exner_500, p_exner_1,
 22 -     layer_thickness, min_thickness, sum_thickness, avg_lapse_rate
 23 +REAL(KIND=real_umphys) :: cp_over_g, p_exner_500, p_exner_50
 24
 25  INTEGER(KIND=jpim), PARAMETER :: zhook_in  = 0
 26  INTEGER(KIND=jpim), PARAMETER :: zhook_out = 1
 27 @@ -164,15 +163,12 @@
 28    j1f = rows - 1
 29  END IF
 30  p_exner_500 = (500.0/1000.0)**kappa
 31 -p_exner_1  =  (1.0/1000.0)**kappa
 32 +p_exner_50  =  (50.0/1000.0)**kappa
 33  cp_over_g = cp / g
 34  dti = ( min_trop_level + max_trop_level ) / 2
 35
 36  ltrop = .TRUE.
 37
 38 -! height above current layer over which average lapse rate will be tested
 39 -min_thickness = 2000.
 40 -
 41  !     ! Compute lapse rate between full levels: equation 3.16, UMDP S1
 42
 43  !$OMP PARALLEL DO SCHEDULE(STATIC) DEFAULT(NONE)                               &
 44 @@ -199,61 +195,28 @@
 45  !$OMP END PARALLEL DO
 46
 47  !     ! 2.  Find level of tropopause, where it is well defined
 48 -!           WMO: "... defined as the lowest level at which the
 49 -!           lapse rate decreases to 2 degC km-1 or less, provided
 50 -!           that the average lapse rate between this level and
 51 -!           all higher levels within 2 km does not exceed 2 degC
 52 -!           km-1."
 53
 54  DO k=min_trop_level+1, max_trop_level
 55
 56 -!kun  ! 'K+1' level for lapse rate test; allows K iteration up to P_LEVELS
 57 -!kun  kp1=MIN(k+1,p_levels)
 58 +  ! 'K+1' level for lapse rate test; allows K iteration up to P_LEVELS
 59 +  kp1=MIN(k+1,p_levels)
 60
 61 -!$OMP PARALLEL DO SCHEDULE(STATIC) DEFAULT(NONE) PRIVATE(i,j,z)                  &
 62 +!$OMP PARALLEL DO SCHEDULE(STATIC) DEFAULT(NONE) PRIVATE(i,j)                  &
 63  !$OMP SHARED(rows,row_length,exner_theta_levels,lapse_rate,ltrop,IT_work,      &
 64 -!$OMP p_exner_1,p_exner_500,k,layer_thickness, min_thickness, sum_thickness, avg_lapse_rate)
 65 +!$OMP p_exner_50,p_exner_500,kp1,k)
 66    DO j = 1, rows
 67      DO i=1, row_length
 68
 69 -      ! check for potential tropopause (TP)
 70 -      IF ( exner_theta_levels(i,j,k-1)  >   p_exner_1 .AND.                   &
 71 +      !         ! Not-quite-WMO criteria for interval containing tropopause
 72 +      !         ! (where 'interval' stretches between layer centres k and k-1)
 73 +
 74 +      IF ( exner_theta_levels(i,j,k-1)  >   p_exner_50 .AND.                   &
 75             exner_theta_levels(i,j,k)  <   p_exner_500 .AND.                    &
 76 -           lapse_rate(i,j,k)  <   lapse_trop .AND. ltrop(i,j) )              &
 77 +           lapse_rate(i,j,k)  <   lapse_trop .AND.                             &
 78 +           lapse_rate(i,j,kp1)  <   lapse_trop .AND. ltrop(i,j) )              &
 79        THEN
 80 -        !kun!ltrop(i,j)=.FALSE.
 81 -        !kun!IT_work(i,j) = k
 82 -        ! potential TP found, check average lapse rate for 2km above current level
 83 -        sum_thickness = 0
 84 -        avg_lapse_rate = 0
 85 -
 86 -        ! average lapse-rate above potential TP layer
 87 -        DO z=k, p_levels
 88 -
 89 -          ! integrate layer thickness
 90 -          layer_thickness = ( t(i,j,z-1) - t(i,j,z) ) / lapse_rate(i,j,z)
 91 -          sum_thickness = sum_thickness + layer_thickness
 92 -          ! sum up lapse rates weighted by layer thickness
 93 -          avg_lapse_rate = avg_lapse_rate + lapse_rate(i,j,z) * layer_thickness
 94 -
 95 -          ! 2km thickness reached
 96 -          IF (sum_thickness >= min_thickness .OR. z == p_levels) THEN
 97 -
 98 -            ! weighted mean lapse rate over interval
 99 -            avg_lapse_rate = avg_lapse_rate / sum_thickness
100 -
101 -            ! check average lapse rate over interval following WMO definition
102 -            IF (avg_lapse_rate <= lapse_trop) THEN
103 -              ltrop(i,j) = .FALSE.
104 -              IT_work(i,j) = k
105 -            END IF
106 -
107 -            !stop lapse rate average calculation and continue with next point
108 -            exit
109 -
110 -          END IF
111 -        END DO
112 -
113 +        ltrop(i,j)=.FALSE.
114 +        IT_work(i,j) = k
115        END IF
116      END DO
117    END DO

```

## DEBUG:
### python_env work in **command line** but fail in **workflow** for the missing of mule.
To find the reason, I add the following code in the script and run it:
```
python_env python -c "import sys; print('\n'.join(sys.path))"
```
and also run it in commandline for comparison.    
The results show as below:
- in workflow:
```
/work/n02/n02/an25872/cylc-run/u-ea539_ants_test/run2/src/ants/lib
/opt/cray/pe/python/3.10.10
/work/n02/n02/an25872/cylc-run/u-ea539_ants_test/run2/bin
/opt/cray/pe/python/3.10.10/lib/python310.zip
/opt/cray/pe/python/3.10.10/lib/python3.10
/opt/cray/pe/python/3.10.10/lib/python3.10/lib-dynload
/work/n02/n02/an25872/myvenv/lib/python3.10/site-packages
/opt/cray/pe/python/3.10.10/lib/python3.10/site-packages
```
- in command line:    
```
/work/n02/n02/an25872/cylc-run/u-ea539_ants_test/run2/src/ants/lib
/opt/cray/pe/python/3.10.10
/work/y07/shared/umshared/lib/python3.9
/work/y07/shared/utils/core/bolt/0.8/modules
/opt/cray/pe/python/3.10.10/lib/python310.zip
/opt/cray/pe/python/3.10.10/lib/python3.10
/opt/cray/pe/python/3.10.10/lib/python3.10/lib-dynload
/work/n02/n02/an25872/myvenv/lib/python3.10/site-packages
/opt/cray/pe/python/3.10.10/lib/python3.10/site-packages
````
We note the key differences is the two pathes:
```
/work/y07/shared/umshared/lib/python3.9
/work/y07/shared/utils/core/bolt/0.8/modules
```
Which is not contained in the system path in workflow.    
Therefore, we add these two pathes into `PYTHONPATH` in python_env.



### AttributeError: 'NoneType' object has no attribute 'GDT_Int16'
I didn't find gdal on ARCHER2. However, the GDAL is in fact not necessary for ants. This error seems to stem from a small flaw in the `ants/fileformats/raster.py`.    

**resolution:**
To fix this flaw, we `fcm co fcm:ancil_ants.xm/tags/2.0.0` as `/work/n02/n02/an25872/FCM/ancil_ants/2.0.0_zikun`. and make the changes as below:
```
                                                                                               33,1          All
diff -ur 2.0.0/lib/ants/fileformats/raster.py 2.0.0_zikun/lib/ants/fileformats/raster.py
--- 2.0.0/lib/ants/fileformats/raster.py        2026-07-03 02:51:56.000000000 +0100
+++ 2.0.0_zikun/lib/ants/fileformats/raster.py  2026-07-09 10:30:10.000000000 +0100
@@ -32,16 +32,18 @@
     )
     warnings.warn(msg.format(str(_GDAL_IMPORT_ERROR)))

-_GDAL_DATATYPES = {
-    "i2": gdal.GDT_Int16,
-    "i4": gdal.GDT_Int32,
-    "u1": gdal.GDT_Byte,
-    "u2": gdal.GDT_UInt16,
-    "u4": gdal.GDT_UInt32,
-    "f4": gdal.GDT_Float32,
-    "f8": gdal.GDT_Float64,
-}
-
+if gdal is not None:
+    _GDAL_DATATYPES = {
+        "i2": gdal.GDT_Int16,
+        "i4": gdal.GDT_Int32,
+        "u1": gdal.GDT_Byte,
+        "u2": gdal.GDT_UInt16,
+        "u4": gdal.GDT_UInt32,
+        "f4": gdal.GDT_Float32,
+        "f8": gdal.GDT_Float64,
+    }
+else:
+    _GDAL_DATATYPES = {}

 class _GdalDataProxy(object):
     """A reference to the data payload of a single gdal raster band."""
Only in 2.0.0: .svn
```
### time dimension of "tropopause" cube is too long for ozone_redistribution
job.err:
```
 33 RuntimeError: The "tropopause" cube does not contain "[12, 24]" months worth of data
 34 [FAIL] python_env python ${CYLC_WORKFLOW_RUN_DIR}/src/contrib/redistribute_ozone.py -t $TROPOPAUSE_INPUT -r $OR    OGRAPHY_INPUT -d $DENSITY_INPUT -z $OZONE_INPUT -o $OZONE_OUTPUT -y $YEAR # return-code=1
```
**reason:**     
Normally, after retrieve_ozone, The 'OZONE_SHARE' directory should only contains the `po stream` files from the previous two years relative to the current timestamp.     
However, for unknown reason, files older than two years are still retained.

**resolution:**    
To resolve this issue, here we add a pre-script to delete the existing links before running the main script in the section [[retrieve_ozone]] which is contained in the ozone-redistribution.rc.
```
 56     [[retrieve_ozone]]
 57         inherit = OZONE, OZONE_ARCHIVE_RESOURCE
 58         pre-script =  """
 59             mkdir -p ${DATAM}
+60             rm -f $OZONE_SHARE/*a.${SOURCE_STREAM}*.pp
 61             """
 62         [[[environment]]]
 63            ROSE_APP_OPT_CONF_KEYS = {{ 'updated' if OZONE_USE_UPDATED_ANCIL else '' }}
 64            PRIMARY_ARCHIVE_SOURCE = {{OZONE_PRIMARY_ARCHIVE}}
 65            SECONDARY_ARCHIVE_SOURCE = {{OZONE_SECONDARY_ARCHIVE}}

```

### Can't find the compressed log on ARCHER2 with CYLC8
On archer2, for cylc8, the history logs are kept on puma2. So try to find them on puma2 `~/cylc-run`.
