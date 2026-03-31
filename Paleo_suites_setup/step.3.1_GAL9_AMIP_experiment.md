## start-point suite on MONSOON3: u-dr521 and  u-cn480
u-dr521 is a GAL9 suite on the internal platform of Meo Office. Take this as a start point, I copy it as **u-dy179**.
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
