Through the above steps, we have successfully run this model for three years. The next issue to be focused on is how to set the correct post-processing.
The outputs of different model components are controled by different setting.

## STASH (Atmosphere) ##
### The definition of STASH (taken from [Storage Handling and Diagnostic System (STASH)	](https://code.metoffice.gov.uk/doc/um/vn13.9/papers/umdp_C04.pdf) ###
The STASH system (standing for Spatial and Temporal Averaging and Storage Handling) embraces those components of the UM responsible for generating versatile and optional model diagnostic ﬁelds for the whole range of Uniﬁed Model conﬁgurations and applications, so that output is in a standard format — the model ﬁeldsﬁle — and new diagnostics can be readily introduced. In order to meet these goals, STASH needs to know the data locations of all prospective data for output, and this is achieved by STASH also controlling the set-up of all data addressing within the model.



`postproc > Atmosphere > process_all_streams`    
find the `STASHmaster` file for the detail of STASH codes:     
The path of STASHmaster file: ~/cylc-run/{your suite}/app/um/file/STASHmaster

finish the [further exercise](https://ncas-cms.github.io/um-training/further-exercises-1.html) about STASH in UM praticals

[Unified Model Output Post Processing](https://code.metoffice.gov.uk/doc/um/vn13.9/papers/umdp_Y01.pdf)

### structure of UM Outputs ###

- UM outputs:
  - [file formats](https://code.metoffice.gov.uk/doc/um/vn13.9/papers/umdp_F03.pdf)
  - [Unified Model Output Post Processing](https://code.metoffice.gov.uk/doc/um/vn13.9/papers/umdp_Y01.pdf)
    - Marker files: ~/cylc-run/{your suite}/work/{cycle point}/coupled/*.done    
      Rose UM suites which are set up to launch subjobs when particular output is produced may ’poll’ for the existence of a .done file which indicates that a file has been closed.
      The UM may be configured to produce a zero-length file with the filename of the original file plus an extension of `.done` when **closing a writable file**. For the same files any pre-existing `.done` file will be deleted whenever the UM opens the file.
      Switches:
      ```
      namelist
      --> IO System Settings
          --> General IO Control
              --> io_external_control
      ```
    - Marker files: ~/cylc-run/{your suite}/work/{cycle point}/coupled/*.arch    
      The UM may be configured to produce zero length files with a .done or .arch file extension to indicate when particular files are finished with. These markers may then be used by post processing tasks such as archiving, housekeeping, external meaning procedures, or to launch dependent tasks within a Rose suite.
      The UM may be configured to produce a zero length file with the original filename plus an extension of .arch in the run directory (typically $CYLC_TASK_WORK_DIR), which will indicate that the UM will not need the file again in future cycles.
      Switches:
      ```
      namelist
      --> IO System Settings
          --> General IO Control
              --> l_postp
      ```
    - MOOSE: Met Office Archive Storage System: long-term data archive system of Met Office
    - Archiving and Deletion:
      ```
      postproc
      --> Atmosphere
          --> Archiving
              --> archive_switch and del_switch
      ```
      Selecting these items will trigger the appropriate options to control which files are to be archived and deleted, and the corresponding frequencies and offsets. The app can also be run in a ‘debug’ mode, in which a log file will be produced indicating which files would be archived and/or deleted, **but without performing the actions**. This can be triggered using
      ```
      postproc
      --> Atmosphere
          --> debug
      ```
  - [STASH (Storage Handling and Diagnostic System)](https://code.metoffice.gov.uk/doc/um/vn13.9/papers/umdp_Y01.pdf)

### link stash codes with variable name
searcher

download the STASH CODE TABLE here(https://reference.metoffice.gov.uk/um/release/_9.1)
https://reference.metoffice.gov.uk/um/stash/_<stash_code_you_want_to_know>


### dumping and meaning ###
Know more about the [dumping and meaning](https://code.metoffice.gov.uk/doc/um/latest/papers/umdp_C05.pdf)
Note that the meaning of UM model is set at
      ```
      um
      --> namelist
          --> model input and output
              --> dumping and meaning
      ```
In contrast, the meaning of NEMO and CICE is set at
      ```
      postproc
      --> NEMO
          --> Diagnostics
              --> meaning
      ```
and
      ```
      postproc
      --> CICE
          --> Diagnostics
              --> meaning
      ```
## NEMO and CICE ##
- NEMO outputs:
  - Some set in nemo_cice don't work (for example ***nn_write***). That may be caused by the IO server

## Debugging instance ##
### number of fields exceeds reserved headers ###
for a error like this: 
```
???????????????????????????????????????????????????????????????????????????????? 
???!!!???!!!???!!!???!!!???!!! ERROR ???!!!???!!!???!!!???!!!???!!! 
? Error code: 4 
? Error from routine: STWORK 
? Error message: STWORK: Number of fields exceeds reserved headers for unit 28 
? Error from processor: 18 
? Error number: 17 
????????????????????????????????????????????????????????????????????????????????
```
Where to find the outputs from specific processor:
archer2:~/cylc-run/{your suite}/work/{the cycle}/atmos_main/pe_output/{your suite}.fort6.pe0000

What to do：
Try to increase the reserved headers for the stream indicated in the error

find the comcrete discussion at: [https://cms-helpdesk.ncas.ac.uk/t/cycle-point-for-restarting-suites/476/19]

### Validity Time Mismatch ###
```
[ERROR]  Validity time mismatch in file /work/n02/n02/an25872/cylc-run/u-ds215/share/data/History_Data/ds215a.ps1850son to be archived
        --> Expected [1850, 12, 1, 0, 0, 0] and got [1850, 9, 1]
```


The key logic here is making your climate meaning settings consistent with your STASH setting.
check your stash stream by switching off `postproc > Atmosphere > process_all_streams`. Try to make them match with each other.

Also see (https://cms-helpdesk.ncas.ac.uk/t/validity-time-mismatch/1773)

## The Climate Data Dissemination System (CDDS) ##
The Climate Data Dissemination System (CDDS) is a Python-based system that manages the reprocessing of HadGEM3 and UKESM1 climate model data into a standards compliant ([CMOR](https://cmor.llnl.gov/)) form suitable for publication and sharing. The primary driver behind CDDS was the CMIP6 project and CDDS was used, and is continuing to be used, to deliver a large amount of data to the [Centre for Environmental Data Archival (CEDA)](http://www.ceda.ac.uk) for publication to ESGF. 

CDDS has recently been adapted to allow for the easy addition of both models and projects, provided that they follow the structure for CMIP6, i.e. via predefined activities (MIPS), source ids (models) and experiments. As of version 2.2.4 CDDS supports production of data for CMIP6 and for a Met Office internal project [GCModelDev](https://github.com/MetOffice/gcmodeldev-cmor-tables) where Met Office scientists are encouraged to request additional activities and experiments that can be used to support their science. Note that the GCModelDev project is not intended to prepare data for publication -- anyone wanting to publish data or prepare it for an external project is encouraged to contact the [CDDS team](mailto:cdds@metoffice.gov.uk) or start a discussion here.

### Could not find metadata of UM name_list in rose editor ###
When you click um in rose, you may get a error as below
```
Could not find metadata for {a directory}
```
This is caused by the default metadata directory shift into a new location:
Change it at `um > meta`, by the following directory
```
/home/n02/n02/ros/meta/moci/CMIP6_rose-meta/um-atmos/HEAD
```







## Some Basic paths ##

### Where Data Are Stored ###

$ROSE_DATAC： where to archive the outputs    
default:cylc_run/{your suite}/share/cycle/   

$ROSE_DATA： where to store all the outputs
default:/cylc-run/u-ds206/share/data    

$DATAM: Directory path relative to $ROSE_DATA.    
default:History_data

STASHC：STASH control ﬁle. This ﬁle is read during model initialisation at run time and converted into
an internal collection of STASH related arrays, in particular a “STASHlist” array STlist that holds all the valid
STASH requests.located at /cylc-run/u-ds206/work/18501201T0000Z/coupled


where to set: Suite conf > Output Paths


