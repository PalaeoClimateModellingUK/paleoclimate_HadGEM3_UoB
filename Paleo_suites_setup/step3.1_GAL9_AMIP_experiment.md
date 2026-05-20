## start-point suite on MONSOON3: u-dr521 and  u-cn480
u-dr521 is a GAL9 suite on the internal platform of Met Office. Take this as a start point, I copy it as **u-dy179**.
There is some necessary modification on the configuration to make it runnable on MONSOON3.

### Testing fails
```
[WARN] Failed to import module: mule_cumf
[WARN]   Traceback (most recent call last):
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/mule/packing.py", line 46, in <module>
[WARN]       import um_packing
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/um_packing/__init__.py", line 21, in <module>
[WARN]       from .um_packing import wgdos_pack, wgdos_unpack, get_shumlib_version
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/um_packing/um_packing.py", line 9, in <module>
[WARN]       __bootstrap__()
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/um_packing/um_packing.py", line 3, in __bootstrap__
[WARN]       import sys, pkg_resources, importlib.util
[WARN]   ModuleNotFoundError: No module named 'pkg_resources'
[WARN]   During handling of the above exception, another exception occurred:
[WARN]   Traceback (most recent call last):
[WARN]     File "/common/share/metomi/cylc-8.6.3-2/lib/python3.12/site-packages/metomi/rose/apps/rose_ana.py", line 655, in _load_analysis_modules
[WARN]       self.modules.add(__import__(module_name))
[WARN]                        ^^^^^^^^^^^^^^^^^^^^^^^
[WARN]     File "/home/users/zikun.ren.ext/cylc-run/u-dy179/run4/ana/mule_cumf.py", line 9, in <module>
[WARN]       import mule
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/mule/__init__.py", line 1788, in <module>
[WARN]       from mule.ff import FieldsFile  # noqa: E402
[WARN]       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/mule/ff.py", line 29, in <module>
[WARN]       from mule.packing import wgdos_pack_field, wgdos_unpack_field
[WARN]     File "/projects/metoff/umdir/mule/mule-2024.11.1/python-3.7.0_numpy-1.20.2/no-openmp/lib/mule/packing.py", line 107, in <module>
[WARN]       raise ImportError(err.args + (msg,))
[WARN]   ImportError: ("No module named 'pkg_resources'", 'SHUMlib Packing library found, but failed to import')
```
The python script `mule_cumf.py` is required by the tasks: TEST_NOOMP, TEST_NOSTASH, TEST_OMP, TEST_PE, and mule_cumf.py require module `pkg_resources` which is not installed in the default python environment for CYLC8
on the MONSOON3. Therefore, turn off all of them. set `TASK_TESTS` to false at `rose-suite.conf`.


## DEBUGGING:
### retrieve_ozone: MOOSE is not able to run on host nidd1128
#### error information
This bug occurred when I was trying to activate the interactive ozone scheme in the AMIP suite u-dy179. The situation is as below:
In `job.err` of the `retrieve_ozone`:
```
Traceback (most recent call last):
  File "/home/users/zikun.ren.ext/cylc-run/u-dy179/run9/bin/retrieve_ozone_data.py", line 485, in <module>
    main()
  File "/home/users/zikun.ren.ext/cylc-run/u-dy179/run9/bin/retrieve_ozone_data.py", line 466, in main
    year_data = get_archived_data(year_data, archive, i + 1)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/users/zikun.ren.ext/cylc-run/u-dy179/run9/bin/retrieve_ozone_data.py", line 352, in get_archived_data
    raise OzoneArchiveRetrievalError(
OzoneArchiveRetrievalError:
[ERROR] Failed in archive retrieval script: /home/users/zikun.ren.ext/cylc-run/u-dy179/run9/bin/retrieve_massr.sh
[FAIL] ants-launch $CYLC_WORKFLOW_RUN_DIR/bin/retrieve_ozone_data.py # return-code=1
2026-05-20T15:13:34Z CRITICAL - failed/ERR
```
In `job.out` of the `retrieve_ozone`:
```
[INFO] Prepending the following to PYTHONPATH: /home/users/zikun.ren.ext/cylc-run/u-dy179/run9/share/fcm_make_ants/build/lib
[INFO] ANTS version loaded was:
ants: /home/users/zikun.ren.ext/cylc-run/u-dy179/run9/share/fcm_make_ants/build/lib/ants/__init__.py (version 2.0.0)
[INFO] Iris version loaded was:
iris: /lustre/ehz2col/collaboration/common/share/scitools/environments/default-2025_11_26/lib/python3.12/site-packages/iris/__init__.py (version 3.14.0)
[OK] Modules loaded.
Using Python version 3.12.12 | packaged by conda-forge | (main, Oct 22 2025, 23:25:55) [GCC 14.3.0]
Using Iris version 3.14.0
[INFO] *************************************************************************
[INFO]
[INFO] Troposphere data to be retrieved from files matching "dy179a.po[1988|1989]*.pp"
[INFO]
[INFO] Ozone redistribution shared task directory:
                /home/users/zikun.ren.ext/cylc-run/u-dy179/run9/share/data/ozone_redistribution
[INFO]
[INFO] Initial cycle point: 19900101T0000Z
[INFO] Current cycle point: 19900101T0000Z
[INFO]
[INFO] Availability on disk expected from model run:
                1988=No, 1989=No
[INFO] Primary archive source:
                moose:crum/u-dy179/apo.pp
[INFO] Secondary archive source:

[INFO]
[INFO] *************************************************************************
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
[INFO] Checking moose:crum/u-dy179/apo.pp archive for 1989, months between 1 and 12.
[SUBPROCESS] [MOOSE] moo select -C query moose:crum/u-dy179/apo.pp dy179a.po1989_arch1.pp
[MOOSE ERR]
```
In `selevt.out` at the work directory of retrieve_ozone:
```
MOOSE is not able to run on host nidd1128
```
#### reason:
The moose command is not allowed to be used on the `collashared` queue on MONSOON3.
#### resolution:
Edit the `meto_ex_ozone.cylc` in the `site` directory and ozone-redistribution.cylc.     
Refer to the [revision 355927](https://code.metoffice.gov.uk/trac/roses-u/changeset/355927) on Trac system.
