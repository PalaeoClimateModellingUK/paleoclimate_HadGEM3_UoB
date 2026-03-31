## start-point suite on MONSOON3: u-dr521 and  u-cn480
u-dr521 is a GAL9 suite on the internal platform of Meo Office. Take this as a start point, I copy it as **u-dy179**.
There is some necessary modification on the configuration to make it runnable on MONSOON3.

### Testing
The python script `mule_cumf.py` is required by the tasks: TEST_NOOMP, TEST_NOSTASH, TEST_OMP, TEST_PE, and mule_cumf.py require module `pkg_resources` which is not installed in the default python environment for CYLC8.
on the MONSOON3. Therefore, turn off all of them. set `TASK_TESTS` to false at `rose-suite.conf`.
