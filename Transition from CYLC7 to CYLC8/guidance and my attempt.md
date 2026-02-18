

[Offical guidance for CYLC upgrading](https://cms.ncas.ac.uk/cylc8/upgrading-workflows/#3-make-cylc-8-changes)

My suite: u-dw917 (a copy of u-di629)


In the test suite, there is a special CYLC variables named `$CYLC_SUITE_DEF_PATH`. it is used in the `app/rebuild_nemo_restart_files` and `redistribute_ozone`.
In CYLC8 it should be changed to `$CYLC_WORKFLOW_RUN_PATH`

For `config_base` in `fcm_make_*` tasks, the fcm: prefix must now be explicitly specified and can no longer be omitted.

u-dw917 is successfuly run under the framework of CYLC8 now.
