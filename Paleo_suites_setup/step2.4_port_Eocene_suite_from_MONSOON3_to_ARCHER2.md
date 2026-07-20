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
+60             rm $OZONE_SHARE/*a.${SOURCE_STREAM}*.pp
 61             """
 62         [[[environment]]]
 63            ROSE_APP_OPT_CONF_KEYS = {{ 'updated' if OZONE_USE_UPDATED_ANCIL else '' }}
 64            PRIMARY_ARCHIVE_SOURCE = {{OZONE_PRIMARY_ARCHIVE}}
 65            SECONDARY_ARCHIVE_SOURCE = {{OZONE_SECONDARY_ARCHIVE}}

```

### Can't find the compressed log on ARCHER2 with CYLC8
On archer2, for cylc8, the history logs are kept on puma2. So try to find them on puma2 `~/cylc-run`.
