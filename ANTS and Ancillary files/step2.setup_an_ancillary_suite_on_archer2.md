## setup the workflow for GC5_N96_ORCA1_ancils:
We check out the suite: `u-cc111/GC5_N96_ORCA1_ancils` as a startpoint of our setup.     
We note that the workflow is composed by twp group of tasks. One for `CAP`(the old workflow to generate ancillary files) and the other for `ANTS`(the new module). The running of ANTS require an python environment with ants packae installed. On the `spice`, it is realized by load module `ants`. In contrast, on archer2, the ants can only be aproached by [singularity](https://cms.ncas.ac.uk/miscellaneous/ants-container/). Therefore, the first step to setup the ancillary suite on archer2 should be apply the `singularity` to the `ANTS` workflow.
### 1. python_env
### 2. enviroment variables.

### DEBUG
#### 1. ERROR related to escape character
In `job.err` of `ancil_soil_roughness`:
```
FATAL:   stat /work/n02/n02/an25872/cylc-run/u-cc111/work/1/ancil_ukca_volcanic_emissions/${CONTRIB_PATH}/AerosolChemistryEmissions/bin/process_emiss_volccont.py: no such file or directory
[FAIL] python_env \${CONTRIB_PATH}/AerosolChemistryEmissions/bin/process_emiss_volccont.py \
[FAIL] ${sources} --destgrid-file ${target_grid} -o ${output} <<'__STDIN__'
[FAIL] 
[FAIL] '__STDIN__' # return-code=255
2026-04-22T17:17:59+01:00 CRITICAL - failed/EXIT
```
**Resolution:**
delete the escape character '\' ahead of `${CONTRIB_PATH}` at `u-cc111/app/ancil_ukca_volcanic_emissions/rose-app.conf`.

#### 2.ERROR with working directory
In `job-activity.log` of `ancil_clim_sst`:
```
[jobs-submit ret_code] 0
[jobs-submit out] 2026-04-22T17:12:21+01:00|1/ancil_clim_sst/07|0|13380161
(ln02) 2026-04-22T17:12:21+01:00 [STDERR] sbatch: Warning: It appears your working directory may not be on the work filesystem. It is /home1/home/n02/n02/an25872. The home filesystem and RDFaaS are not available from the compute nodes - please check that this is what you intended. You can cancel your job with 'scancel <JOBID>' if you wish to resubmit.
(ln02) 2026-04-22T17:12:21+01:00 [STDOUT] Submitted batch job 13380161
[jobs-poll ret_code] 0
[jobs-poll out] 2026-04-22T17:16:49+01:00|1/ancil_clim_sst/07|{"batch_sys_name": "slurm", "batch_sys_job_id": "13380161", "batch_sys_exit_polled": 1, "time_submit_exit": "2026-04-22T17:12:25+01:00"}
```
**Reason:**     
The tasks can only be submitted to standard queue under the work filesystem
**Resolution:**    
add `--chdir=/work/n02/n02/{{USERNAME}}` to the [[SLURM]][[[directives]]]

#### 3.ERROR reading VEGFRAC namelist for task `ancilVegfrac`
job.out
```
 No. of land points  10678
********************************************************
###CALCULATING VEGETATION FRACTIONS AND SOILS PARAMETERS
###USING IGBP VEGETATION CLASSIFICATION DATASET
********************************************************
 ***ERROR reading VEGFRAC namelist
```
job.err
```
+ export 'FILENV=main_assign'
+ assign -R
+ assign -a /work/y07/shared/umshared/ancil/atmos/master/vegetation/cover/igbp/v2/qrdata.igbp -s bin u:56
+ echo 'srun --distribution=block:block --hint=nomultithread -n 1 /work/y07/shared/umshared/CAP9.1/build/bin//central_ancillary.exe'
+ srun '--distribution=block:block' '--hint=nomultithread' -n 1 /work/y07/shared/umshared/CAP9.1/build/bin//central_ancillary.exe
gc_abort (Processor     0): ANCIL EXIT
MPICH ERROR [Rank 0] [job id 13393664.0] [Thu Apr 23 18:50:41 2026] [dvn01] - Abort(9) (rank 0 in comm 0): application called MPI_Abort(MPI_COMM_WORLD, 9) - process 0

srun: error: dvn01: task 0: Exited with exit code 9
srun: launch/slurm: _step_signal: Terminating StepId=13393664.0
[FAIL] AncilScr_RoseVegfrac <<'__STDIN__'
[FAIL]
[FAIL] '__STDIN__' # return-code=9
2026-04-23T18:50:42+01:00 CRITICAL - failed/EXIT
```
This error happened in the running process of CAP. To fix it, we refer to the source codes of CAP.
And we find that:    
The mismatch bettween the given `vegfrac.nl` and the namelist `VEGFRAC` defined in `/work/y07/shared/umshared/CAP9.1/extract/ancil/src/code/igbp_general/igbp.F` can be the reason underlying this error.     
`/work/y07/shared/umshared/CAP9.1/extract/ancil/src/code/igbp_general/igbp.F`:
```
182
183       NAMELIST /VEGFRAC/IMETHOD,FRAC_DIAG,LANDICE_FRAC,
184      &                  SEASONAL,LCHECK_LANDICE,NO_ADJ_LANDICE,
185      &                  USE_MODIS_LAI,USE_LAND_FRAC,ANTARCTICA_ALLICE,
186      &                  IGBP_VEG_OVERRIDE,USE_MODIS4_LAI, OFFSETX,
187      &                  OFFSETY
188
```
`vegfrac.nl` `in app/ancilVegfrac/rose-app.conf`
```
129 [namelist:vegfrac]
130 antarctica_allice=.true.
131 frac_diag=.false.
132 igbp_veg_override=.false.
133 imethod=4
134 l_region_reset=.false.
135 landice_frac=0.5
136 lcheck_landice=.false.
137 no_adj_landice=0
138 offsetx=1
139 offsety=1
140 region_reset=60.0,-60.0,80.0,-45.0
141 reset_type=17
142 seasonal=.true.
143 use_land_frac=.true.
144 use_modis4_lai=.true.
145 use_modis_lai=.false.
```
- **Resolution:**
  ignore `l_region_reset`, `region_reset`, and `reset_type`. The version of CAP may be the root of the error.

