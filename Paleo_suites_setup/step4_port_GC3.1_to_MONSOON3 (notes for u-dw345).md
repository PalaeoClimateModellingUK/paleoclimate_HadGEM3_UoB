## PI
From u-di622 (ARCHER2) to u-dw345 (MONSOON3)
Referece suite:u-dv344
### DEBUG
#### FCM_MAKE_PP
```
[FAIL] fcm:nemo: location keyword not defined

[FAIL] fcm make -f /lustre/ehz2col/collaboration/data/scratch/zikun.ren.ext/cylc-run/u-dw345/work/22010101T0000Z/fcm_make_pp/fcm-make.cfg -C /home/users/zikun.ren.ext/cylc-run/u-dw345/share/fcm_make_pp -j 4 mirror.target=login:cylc-run/u-dw345/share/fcm_make_pp mirror.prop{config-file.name}=2 # return-code=255
```
To fix this Error, I transformed all the configuration of postproc2.4 into postproc2.5.
#### deadlock with no error information

Each components need to be consistent:
GCOM, MPI, module list(GC3->GC5), CYLC, UM source codes.


```
[SUBPROCESS OUTPUT] nn_it000=1,
[SUBPROCESS OUTPUT] nn_itend=1920,
[SUBPROCESS OUTPUT] rn_rdt=1350.0,
[LINK_DRIVERS] attemting to run with command: mpiexec --cpu-bind=depth -n 384 -d 2 ./atmos.exe : -n 128 -d 1 ./ocean.exe : -n 6 -d 1 ./xios.exe 
[DRIVER_TEST_SCRIPT] Drivers successfully linked
```
delete the `--cpu-bind=depth` .. in the UM_RESOURCE at {site}.rc

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!     
check the fcm_make2_drivers
examine `~/cylc-run/u-dw345/share/fcm_make_drivers/build/bin`
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!          
I used the restart files from u-di622 (Eocene suite) for the piControl. That is a **huge mistake**.     
Fix it, once the outage end.

