## What is ANTS?
[Central Ancillary Program and Ancillary tools and Suites (ANTS)](https://code.metoffice.gov.uk/trac/ancil)      
### ANTS
ANTS is a Python library, primarily built on top of Iris. Its intent is to make the implementa􀆟on of
ancillary science rou􀆟nes easier, more transparent than previous incarna􀆟ons, and provide a
standardised set of tooling. ANTS, as a library, does not concern itself with specific ancillary science
rou􀆟nes, rather aiming to make common opera􀆟ons available via the ANTS library. It is maintained and
developed by a team of Scien􀆟fic So􀅌ware Engineers within the Model Inputs and Outputs team at
the Met Office.
### Contrib
Contrib is a central loca􀆟on for the lodging of ANTS based ancillary science rou􀆟nes. The codes in there
are owned by scien􀆟sts, with technical advice and assurance provided by the ANTS developers. A rosestem
workflow exists in contrib to enable routine testng of the ancillary science rou􀆟nes lodged there
(Apps) as core library updates are made.

## How to use ANTS?
Follow the [Git-Hub page of ANTS install](https://github.com/MetOffice/ANTS/blob/edff7e6bcfe22dead0480a67977169dfb15fdd37/INSTALL.rst#L34) to setup the ANTS.    
[cylc and rose on JASMIN](https://help.jasmin.ac.uk/docs/workflow-management/rose-cylc-on-jasmin/): `export PATH=/apps/jasmin/metomi/bin:$PATH`    
[ANTS on JASMIN(Reading)](https://research.reading.ac.uk/landsurfaceprocesses/software-examples/ants_on_jasmin/)     
[ANTS user guide](https://metoffice.github.io/ANTS/)     
[ANTS of NCAS](https://cms.ncas.ac.uk/miscellaneous/ants-container/)

For the MetOffice's codes transition from FCM to Git. There are actually two group of coding resources. One on [Trac system](https://code.metoffice.gov.uk/trac/home/wiki/ProjectList) and the other on the [GitHub](https://github.com/MetOffice/). Some internal resources on GitHub are still only accessible to MetOffice staff, Therefore, if we want to access these resources (like the `ancillary-file-science` highlighted by the guidance) we still need to turn to [the ancil documentation page on the Trac system](https://code.metoffice.gov.uk/doc/ancil/ants/2.2/index.html).

### ANTS on JASMIN
ANTS has been installed on JASMIN. Use `module load ants` to load the ANTS.  
[structure of ANTS python module](https://metoffice.github.io/ANTS/_modules/)
ancil directory on JASMIN:`/gws/nopw/j04/umshared/umshared/ancil`

### ANTS on ARCHER2
```
export ANTS_CONTAINER=/work/y07/shared/umshared/ANTS/latest/ants_latest.sif
singularity shell $ANTS_CONTAINER
```
u-cc111 seems to be the official suite to generate all the suites for GC5 mdoel.    
use `rosie co u-cc111/GC5_N96_ORCA1_ancils` to check out it.    

Another Ancillary suite might be useful can be `u-by395`. According to the NCAS, [this suite is an official suite, the Regional Nesting Suite, which has been ported to archer2](https://cms-helpdesk.ncas.ac.uk/t/porting-ancillary-suite-to-archer2/1510).    
try the brach `GAL9_Glomap_ancils`    
`rosie co u-by395/GAL9_Glomap_ancils`   
On jASMIN run it by:
`rose suite-run -O ncas`    
