

## The workflow of HadGEM3

Every successfully operated simulation of HadGEM3 should be a well-organized cylc workflow combined with a bunch of interdependent tasks. These tasks are scheduled in sequence and grouped to archive different target.

### cycle point
The time cycle point is the biggest unit of the HadGEM3 workflow. And the cycle point is usuualy regulated by the model time (set at rose_suite.conf[jinja2:suite.rc] by CALENDAR).  Visit **step3.1** to know more detail. The workflow of HadGEM3 will push forward with the cycle containing modeling and post-process of initial output. That means the tasks can always be repeatedly submited and running during a workflow.

The frequency and the total number (the length of the modeling) of the cycle points can be set by `RESUB`(Cycling frequency in GUI), and `RUNLEN` （Total Run Length） at `suite conf >> Run Initialisation and cycle`, respectively.


### Tasks
A cycle point of HadGEM3 typically contains the `couled` task and the `postproc` tasks of different components. At the initial cycle point, there will be additional tasks (such as `install_ancil` and `fcm_make_*`) to build the executable program.    
Note that, All the tasks above is not neccessary in the workflow. They can be canceled in the schedule at ``suite conf >> Build and Run`

## [Jobs acountting](https://ncas-cms.github.io/um-training/running.html#um-standard-output)
For a suite in running, the tasks documented in a suite will be built into some specific jobs.
The `sacct` command displays accounting data for all jobs that are run on ARCHER2. sacct can be used to find out about the resources used by a job. For example; Nodes used, Length of time the job ran for, etc. This information is useful for working out how much resource your runs are using. You should have some idea of the resource requirements for your runs and how that relates to the annual CU budget for your project. Information on resource requirements is also needed when applying for time on the HPC.    
Let’s take a look at the resources used by your copy of u-cc519 run.
- Locate the SLURM Job Id for your run. This is a 6 digit number and can be found in the `job.status` file in the cylc task log directory. Look for the line `CYLC_BATCH_SYS_JOB_ID=` and take note of the number after the `=` sign.
Run the following command:
```
sacct --job=<slurm-job-id> --format="JobID,JobName,Elapsed,Timelimit,NNodes"
```
Where `<slurm-job-id>` is the number you just noted above. You should get output similar to the following:
```
ARCHER2-ex> sacct --job=204175 --format="JobID,JobName,Elapsed,Timelimit,NNodes"
       JobID    JobName    Elapsed  Timelimit   NNodes
------------ ---------- ---------- ---------- --------
204175       u-cc519.a+   00:00:23   00:20:00        1
204175.batch      batch   00:00:23                   1
204175.exte+     extern   00:00:23                   1
204175.0     um-atmos.+   00:00:14                   1
```
The important line is the first line.
- How much walltime did the run consume?
- How much time did you request for the task?
- How many CUs (Accounting Units) did the job cost?
1 node hour currently = 1 CU. See the ARCHER2 website for information about the CU.
There are many other fields that can be output for a job. For more information see the Man page (`man sacct`). You can see a list of all the fields that can be specified in the `--format` option by running sacct `--helpformat`.

## The Unified model
[Possible the newest user guide for UM](https://code.metoffice.gov.uk/doc/um/latest/um-training/index.html)    
[Perfact Slides to learn the UM](https://cms.ncas.ac.uk/assets/docs/UM_Introduction_Presentations.pdf)     
The Met Office Unified Model (UM) is the name given to the suite of numerical modelling software developed and used at the Met Office for forecast prediction across a range of timescales; from weather forecasting to climate change.

Prior to UM9.0 the UM had its own gui, the **UMUI**, which was able to control the configuration and submission of UM jobs. As of UM9.0 the UM is configured, built, jobs submitted and run by **Rose**. **Thus a prerequisite of this UM course is that trainees are familiar with Rose**. 

## [Unified Model Documentation Papers](https://code.metoffice.gov.uk/doc/um/latest/umdp.html)
The link above supplied many useful documents about the configuration of UM, such as:
- C02 Coupled Model Technical Overview
- C04 Storage Handling and Diagnostic System (STASH)

## [FCM tutorial](https://ncas-cms.github.io/um-training/fcm-tutorial.html#fcm-tutorial)
If you want to know more about the fcm sources used in the rose suite, for instance:`fcm_make –> env –> sources`, You will need the page above.

Subversion (SVN) is a centralized [version control system](https://svnbook.red-bean.com/en/1.8/svn.basic.version-control-basics.html) that records changes to files and directories, enabling collaboration, revision tracking, and recovery of earlier versions. It relies on a central repository that all users connect to.

[FCM (Flexible Configuration Management)](https://metomi.github.io/fcm/doc/user_guide/getting_started.html) is a toolset built on top of SVN, developed by the UK Met Office to simplify scientific software development. It provides an easier interface for creating and managing branches, merging changes, and includes a build system (fcm make) to compile large models like the Unified Model or NEMO. In short, SVN is the underlying version control system, while FCM adds extra tools and user-friendly features tailored for scientists and complex model workflows.

### Trac System
Trac system is where you can find the revision from you and others. It supplied convinient tool to compare different suites and codes.
All your revision commited for the suites or the fcm_resources can be viewed and found in the following two links.   
[Trac system for um](https://code.metoffice.gov.uk/trac/um)  
fcm:um.xm/    
[Trac system for NEMO](https://code.metoffice.gov.uk/trac/nemo/)   
fcm:nemo.xm/    
[Trac system for rose suites](https://code.metoffice.gov.uk/trac/roses-u/browser/)   
control by rosie    
[Trac system for Met Office Coupling Infrastructure MOCI](https://code.metoffice.gov.uk/trac/moci/)
fcm:moci.xm/    
[Trac system for GCOM](https://code.metoffice.gov.uk/trac/gcom/)
fcm:gcom.xm/

### Some useful command
- fcm ls fcm:um.x-br/    
   List directory entries in the repository fcm:um.x-br
- fcm branch-info    
  show the detail of present working copy
- fcm branch-list  fcm:um.x    
  show your revision
- fcm branch-delete     
  delete the working copy
- fcm branch-create -k 702 MONSOON3_ozone_fix fcm:moci.x-tr@5515
  make a branch from the trunk
  


## [Solving Common UM Problems](https://ncas-cms.github.io/um-training/solving-problems.html)
### reload and re-trigger ###
In the debugging processes, we always stop and re-run the suites over and over again. However, it is possible to reload the suite definition and then re-trigger the failed task without first stopping the running suite. To do this change to the suite directory:
```
puma2$ cd ~/roses/<suitename>
```
We then reload the suite definition by running the following Rose command:
```
puma2$ rose suite-run --reload
```
Wait for this command to complete before continuing. Finally in the Cylc GUI right-click on the failed task and select `Trigger (run now)`. The `fcm_make_um` task will then submit again.

### [Known Failure point](https://code.metoffice.gov.uk/trac/um/wiki/KnownUMFailurePoints) ###

find some known Failure point of UM by the link above.



## run the model more effectively ##

Navigate to `um –> namelist –> Top Level Model Control –> Run Control and Time Settings`.
Set `ltimer` to `True`. Timer diagnostics outputs timing information and can be very useful in diagnosing performance problems.

Additionally, To output more information about the IU rates, switch on “IO timing” at `um –> namelist –> IO System settings –> General IO Control` .
IO timing information provides a low level diagnostic of data transfer rates. It may be used as a tool to assist investigations in model run timings.

### [Change the processor decomposition](https://ncas-cms.github.io/um-training/further-exercises-1.html#change-the-processor-decomposition) ###

Navigate to `suite conf –> Domain Decomposition –> Atmosphere`.
What is the current processor decomposition?
Why is this not a good way to run the model?

Running “under populated”, i.e. with fewer than the total cores per node, gives access to more memory per parallel task.
"fully populated"

### [Change the model output logging behaviour](https://ncas-cms.github.io/um-training/further-exercises-1.html#change-the-model-output-logging-behaviour) ###
For coupling run, it is very important which component is relative slower than the others. Therefore, sometimes we need detailed log from the coupler (OASIS3).     
- Switch on the item `l_oasis_timers` at `um > namelist > Coupling > Main coupling controls`.    

- Change its log printing by `$NLOGPRT` at `${your suite}/app/coupled/file/namcouple`
`
 75 ###########################################################################
 76  $NLOGPRT
 77 # Index of printing level in output file cplout: 0 = no printing
 78 #  1 = main routines and field names when treated, 30 = complete output
 79   0
 80  $END
`

## [LRUN, CRUN, NRUN](https://code.metoffice.gov.uk/trac/moci/wiki/ModelRestartability) ##
<img width="888" height="608" alt="image" src="https://github.com/user-attachments/assets/51d708c5-e3e8-44aa-8011-c309e934f01b" />
This shows running a model for 30 days. Three "modes" are run:
- LRUN: The LRUN or "long" run, has a 10 day cycle length, so runs 3 cycles of 10 days.
- CRUN: The CRUN or "continuation" run, has a 5 day cycle length, so runs 6 cycles of 10 days.
- NRUN: The NRUN or "new" run also has a 5 day cycle length, so runs 6 cycles of 10 days.
The key difference between the modes is whether each cycle continues the previous cycle, or is considered a new run. LRUN and CRUN **continue** the previous cycle, so the timestep number follows on. Each NRUN takes the state of the model at the end of the previous cycle as the initial conditions for a new run, starting at timestep 1.

If you want to restart your run. There are two forms you can select depending on the issue you encountered.    
If your job is currently running but has failed at some point. You may be able to find the problem and fix and then continue running from some point before the failure as a CRUN. In this case you should follow the section **Existing run**.     
If you would like to start a model run from an output state of a previous run, you will need to setup the model to start from the output of the previous run as an NRUN. In this case you should follow the section **Starting from dumps/restart files**.     

### [Existing run](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Restarting) ###

For existing run, the restart file of UM is set by `CHECKPOINT_DUMP_IM`  in `$DATAM/<runid>.xhist` (usually located at `~/cylc-run/u-ds206/share/data/`). 
The restart file of NEMO should be automatically set as the restart file with latest cycletime. 
The restart file of CICE should be set in the `${CICEDATA}/ice.restart_file` (usually located at `~/cylc-run/u-ds206/share/data/CICEhist`) 

#### [Useful rose suite-run Arguments](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Usefulrosesuite-runArguments) ####

#### [Restarting Failing Suites](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#RestartingFailingSuites) ####

#### [Restarting if the model blows up](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Restartingifthemodelblowsup) ####

#### [Restarting from archived restarts](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Restartingfromarchivedrestarts) ####

Some important notes:   
- Point to the passive tracer restart: ocean_passive_tracers > env > Initialisation Settings        
    - If you don't want the CFCs and Age tracer to be reset (normal case following a failure):
        - Make sure that Init CFC-Age dump=false: suite conf > Build and Run
        - Point Passive tracer restart file (TOP_START) to your restart file
        - Set Restart file to initialize (TOP_TO_INIT) to a dummy string like "not-used" to make it clear that it has not been used.
    - If you need to re-initialize the CFCs and/or the Age tracer:
        - Make sure that Init CFC-Age dump=true: suite conf > Build and Run
        - Point Restart file to initialize (TOP_TO_INIT) to the restart file to be re-initialized
        - Set Passive tracer restart file (TOP_START) as ${ROSE_DATA}/${RUNID}o.restart_trc.nc
- Switch off UM reconfiguration to prevent the atmosphere and land fields from being re-initialised: suite conf > Build and Run > Run reconfiguration -> false
- Switch on bit-comparison options (see NRUN-bitcomp section below)

Note: if NRUNning from January restart files, take care with seasonal and annual means:    

- In standard HadGEM3 and UKESM1 suites prior to vn11.2, the first seasonal mean after a January NRUN will be MAM, and the first annual mean will begin after 11 months (Dec to Nov). Thus if using January restarts you should go back to the start of the year before the failure to avoid gaps in annual or seasonal means.
- At vn11.2 and above the standard HadGEM3 and UKESM1 suites should be able to make use of the December monthly mean from the preceding failed run, and you can therefore start from the most recent January dumps available (unless you restart with rose suite-run --new, in which case you are in the same situation as pre-vn11.2).


#### [Starting one run using restarts from another](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Startingonerunusingrestartsfromanother)

If you need to change the year from that in the restarts (e.g. a CMIP6 historical run branching from the year 1950 of piControl needs to have a start date of 1850), then you must change the validity time in the UM restart to match the required start date (NEMO and CICE don't check the dates in their restart files). There is a mule script to carry this out, and it can be found by checking out from the MOCI repository using the command:
```
fcm export fcm:moci.x_tr/Utilities/lib/umdump_year.py
```
and running with
```
./umdump_year.py <infile> --year=1850
```
If you need to change month or day as well, copy this script and modify as appropriate. Please note that this script requires Mule to be available. Please see instructions above under [Mule Utilities](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#/Mule)

If starting from ocean and atmosphere restarts which are not consistent with each other (i.e. they do not come from the same date of the same run), you need to set `BITCOMP_NRUN=false` in the top-level conf (see `NRUN-bitcomp section` below) to ensure that the ocean iceberg initialisation is performed (`ln_iceshelf_init_atmos=.true.`). Otherwise, the ocean and atmosphere prognostic values for the ice mass may be inconsistent, resulting in no iceberg calving or a huge pulse on the first coupling timestep, either of which will lead to errors in the ocean supply of freshwater.

#### [Bit comparison between NRUN and CRUN](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#BitcomparisonbetweenNRUNandCRUN) ####

#### [Troubleshooting](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Troubleshooting) ####

### [Restart as a new run](https://code.metoffice.gov.uk/trac/moci/wiki/ModelRestartability#Startingfromdumpsrestartfiles) ###

choose one of the following depending on whether the recon task is used
recon ON ainitial=$RESTART_DUMP
recon OFF astart=$RESTART_DUMP
### My Conclusion ###
- If you want continue your run as similar to your old one as possible. You'd best switch on `BITCOMP_NRUN` and switch off `reconfiguration`. Note that `BITCOMP_NRUN` is not necessary, because it only works for the atmospheric component. But switching off `reconfiguration` is necessary if you don't want to make some potential reinitialization in your new suite.

- [Know more about the reconfiguration](https://code.metoffice.gov.uk/doc/um/latest/papers/umdp_000.pdf)

## [Useful Hints & Tips for running with Rose/Cylc on the Archer2](https://cms.ncas.ac.uk/rose-cylc-hints/#passing-arguments-to-fcm_make)


