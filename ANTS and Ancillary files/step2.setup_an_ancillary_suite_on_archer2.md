## setup the workflow for GC5_N96_ORCA1_ancils:
We check out the suite: `u-cc111/GC5_N96_ORCA1_ancils` as a startpoint of our setup.     
We note that the workflow is composed by twp group of tasks. One for `CAP`(the old workflow to generate ancillary files) and the other for `ANTS`(the new module). The running of ANTS require an python environment with ants packae installed. On the `spice`, it is realized by load module `ants`. In contrast, on archer2, the ants can only be aproached by [singularity](https://cms.ncas.ac.uk/miscellaneous/ants-container/). Therefore, the first step to setup the ancillary suite on archer2 should be apply the `singularity` to the `ANTS` workflow.
### 1. python_env
### 2. enviroment variables.

### DEBUG
#### ERROR related to escape character
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

#### ERROR with working directory
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





