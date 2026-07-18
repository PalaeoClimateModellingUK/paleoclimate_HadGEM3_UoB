In step2.2 we already built a Eocene suite on MONSOON3 (u-dv769). However, for the resource limitation on MONSOON3, the simulation runs very slow.     
Therefore, we have a go to port the suite to ARCHER2.

The biggest challenge is the setting up of the python_env, which is used for the interactive ozone, on the ARCHER2. Different with MONSOON3, on archer2 there is no existing module like scitools or um_tool can be directly used in python_env (cray-python helps a lot, but it don't have iris). To make the ozoen scheme runnable on ARCHER2. I set up a python_env as below:

