# From piControl to Eocene
In this section, we try to repoduce the Eocene configuration from piControl configuration step by step.
My start point is the piControl suite **u-ch089**, and the destination is the final product run from Seb (**u-di625**).
## First attempt (possiblly outdated, start:21/8/2025; final edit: 25/8/2025)
Target: Make sure the original suite can work with all standard boundary/initial conditions, before making it Eocene .

### Necessary sub-steps are shown below:
1. copy the **u-ch089** from rosie, to get a new suite (for me it is **u-ds203**).
2. Edit this suite by `rose config-edit u-cm430`:  
  -   at `suite conf >> Project accounting` set the `Account group for HPC tasks` as `'n02-ncas'`  
  -   at `suite conf >> Machine Options` set the `USERNAME` as `Your HPC USERNAME`
  -   at `suite conf >> Machine Options` set the `Science Configuration Module Name` as `GC3-PrgEnv/v1`
  -   at `fcm_make_ocean >> env >> NEMO Source` set the `nemo_sources` as `/home/n02/n02/ros/nemo/branches/dev_r5518_GO6_package`
3. Edit `host` under `$suite_DIR/site/archer2.rc [[HPC]][[remote]]` as `$(rose host-select archer2)`
4. Edit `host` under `$suite_DIR/suite.rc [runtime][[PPTRANSFER]][[[remote]]]` as `$(rose host-select archer2)`
5. Edit `$suite_DIR/site/archer2.rc`:
  -   set `[[HPC]][[[remote]]]` as `$(rose host-select archer2)`
  -   set `[[EXTRACT_RESOURCE]][[[remote]]]` as `$(rose host-select {{ EXTRACT_HOST }})`
  -   at [[POSTPROC_RESOURCE]]pre-script delete:
      ```
      module load um
      module swap craype-network-ofi craype-network-ucx
      module swap cray-mpich cray-mpich-ucx
      ```
      add:
      ```
      module load cce/15.0.0
      module load atp
      ```
6. confirm the restart/initial file of UM (`ainitial at um >> file >> namelist >> reconfiguration and ancillary Control >> `), CICE and NEMO (`NEMO start dump`, `NEMO icebergs start dump` and `CICE start dump` at `suite conf >> Run Initialisation and Cycling >> General technical ooptions`) exist
7. at `$suite_DIR/app/ocean_passive_tracers/rose-app.conf` set `meta` as `/home/n02/n02/ros/meta/ocean_passive_tracers`
8. at `$suite_DIR/app/fcm_make_pp/rose-app.conf` set `install_host` as `remote.cfg`
9. at `$suite_DIR/app/fcm_make_um/rose-app.conf`:  
   - change `config_root_path=/home/ros/um/branches/vn10.7_archer2_compile`
     by `config_root_path=fcm:um.xm-br/dev/simonwilson/vn10.7_archer2_compile`
   - add `branches/dev/jeffcole/vn10.7_archer2_fixes` after `um_sources=branches/pkg/Share/vn10.7_CMIP6_production_mods@43043` 

10. Run the rose suite by `rose suite-run` at the directory of the suite.

Apart from the above steps, there are some other changes made in `u-ds203`. If you want to check all the changes made in u-ds203 compared to `u-ch089`. please check out these two suites from rosie, then compare them use the command line below:
```
diff -ur ~/roses/u-ds203 ~/roses/u-ch089
```


### some tips:
- $UMDIR is set in `$suite_DIR/site/archer2.rc [[HPC]][[enviroment]]` 
- the initial/restart file of Unified model can be set by `ainitial` under `um >> file >> namelist >> recon_technical`
- use `fcm ls fcm:um.xm_br/pkg/Share` to show the
- set `RECON` as true to initialize the run, as false to restart the run
- A tool for checking th e
- The `sacct` command displays accounting data for all jobs that are run on ARCHER2.
  - `sacct` can be used to find out about the resources used by a job. For example; Nodes used, Length of time the job ran for, etc. This information is useful for working out how much resource your runs are using. You should have some idea of the resource requirements for your runs and how that relates to the annual CU budget for your project. Information on resource requirements is also needed when applying for time on the HPC.
- use `fcm commit` to commit the changes for a local suite to the repository.

### Results:
After these modification, this suite successfully run by one model month, then it broke down. Furthermore, the outputs of this run seems to be  
I haven't found the reason yet.


## second attempt (start:25/8/2025)
I found the template I referenced (`u-ch089`) may be a old-fashion one. So I try to find a piControl suite with a later date, which can work with less modification. Fortunately, I quickly find the suite ———— `u-df570` (a recent suite owned by charliewilliams). I copied it, and successfully run it with little change. Additionally, there is no massive difference in configuration between the u-df570 and u-ds203 (my std piControl).
My next step is to figure out the reasons causing the different proceeding.
### sub-steps are shown below:
1. Copy the **u-df570** from rosie, to get a new reference suite (for me it is **u-ds206**), and make some basic modification to run it successfully.
2. Copy the old piControl suite **u-ds203**（use `fcm commit` to synchronize the local suite with the repository）, get the a new piControl suite **u-ds215**.
3. `diff -ur ~/roses/u-ds213 ~/roses/u-ch206 > diff_u-ds206_u-ds213` to find the possible key sets.
4. Through some attempts, we find a few key configuration to be modified:
   - Firstly, except for sequence and dependences set at the [[scheduling]] insuite.rc, There is another element could stop the workflow. To prevent the fastest tasks in a suite from getting too far ahead of the slowest ones, the [`Runahead limiting`](https://cylc.github.io/cylc-doc/7.8.8/html/running-suites.html#runahead-limiting) is set in the cylc.   
     The preferred runahead limiting mechanism restricts the number of consecutive active cycle points. The default value is three active cycle points; see [[scheduling] -> max active cycle points](https://cylc.github.io/cylc-doc/7.8.8/html/appendices/suiterc-config-ref.html#max-active-cycle-points). Alternatively the interval between the slowest and fastest tasks can be specified as hard limit; see [[scheduling] -> runahead limit](https://cylc.github.io/cylc-doc/7.8.8/html/appendices/suiterc-config-ref.html#runahead-limit).
     Therefore, the failing of pp_transfer and post-processing can both stop the workflow. 
   - at `suite conf >> Build and Run` set the `PP_transfer` as `false`. This setting should be temporary. We will open it once we have the login service of JASMIN for P2F project open.
   - at `$path_of_rose_suite/site/archer2.rc[[POSTPROC_RESOURCE]]` revise the `load $UMDIR/modulefiles/postproc/2020.12.11` the pre-script as `module load $UMDIR/modulefiles/postproc/2020.12.11`
5. The interval bettween time cycles must be in line with the postproc seting. For example, if the interval bettween time cycles is one month, you should not set the  create_annual_mean as true.

## Why the u-ds203 fail after processing for one model year?
the rolling of the cycle points are blocked by the limitation of cycle numbers in the task pool










