## start-point suite on MONSOON3: u-dr521 and  u-cn480
u-dr521 is a GAL9 suite on the internal platform of Meo Office. Take this as a start point, I copy it as **u-dy179**.
There is some necessary modification on the configuration to make it runnable on MONSOON3.

### Testing
The python module mule_cumf is required by the tasks: TEST_NOOMP, TEST_NOSTASH, TEST_OMP, TEST_PE, but this module seems to be not installed 
on the MONSOON3. Therefore, turn off all of them. set `TASK_TESTS` to false at `rose-suite.conf`.
