### GC5 Configuration overview
[GC5-accessment](https://code.metoffice.gov.uk/trac/gmed/wiki/Assessment/GC5Assessment)

[From GC3 turn to GC5 on PUMA2](https://cms.ncas.ac.uk/cylc8/)
## Some Reference suites
### ARCHER2
high-resolution configuration(N216-ORCA025): u-da412（my copy: u-ds758）

low-resolution configuration(N96 ORCA1):u-do332；u-dv935


### MONSOON3
low-resolution configuration(N96 ORCA1):u-du021    
low-resolution configuration(N96 ORCA1; GC5-central?):u-dv308, u-dv344

My copy: u-dv344 (GC5-central)



## Some command transition
rose suite-run --local-install-only -> cylc install  
cylc run -> cylc vip   
cylc gui -> cylc tui  

use cylc config <flow-name> to generate a file like suite.rc.processed

see command at：
https://cylc.github.io/cylc-doc/stable/html/user-guide/cheat-sheet.html

## some set transition
default archive directory in gc3.1:
$ROSE_DATAC：cylc_run/{your suite}/share/cycle/

default archive directory in gc5:
$ROSE_DATAC：{your work directory}/ARCHIVE/{your suite}





