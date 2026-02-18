# restarting/branching HadGEM3 runs

Different procedures necessary depending on the type of restart. These notes closely follow the [official documentation](https://code.metoffice.gov.uk/trac/moci/wiki/tips_CRgeneral#Restartingfromarchivedrestarts) and are added once I tried them and confirmed that they work. In general, we need up to 5 different restart files for the different modules:

**1. UM atmosphere**; e.g. aq853a.da25940101_00
**2. NEMO physics**; e.g. aq853o_25940101_restart.nc
**3. NEMO tracer**; e.g. aq853o_25940101_restart_trc.nc
**4. NEMO icebergs**; e.g. aq853o_icebergs_25940101_restart.nc
**5. CICE**; e.g. aq853i.restart.2594-01-01-00000.nc

### restarting from archived restarts
1. Find consistent set of the restart files from the same date, e.g. all files for my archived runs can be found on JASMIN at `/home/users/ssteinig/pmip4_gws/<SUITEID>/<YEAR>0101T0000Z`. These directories contain restart files for 01 January of the current year and 01 December of the last year that can easily be transferred to e.g. [[NEXCS-MONSooN]]. If you want to restart somewhere in-between these yearly files you need to use files from the last cycle that are still on [[NEXCS-MONSooN]]. In my case these would be at `/home/d02/ssteinig/cylc-run/<SUITEID>/share/data/History_Data` (with subdirectories `CICEhist` and `NEMOhist`). NEMO restart files are split across the individual processors (e.g. `<SUITEID>o_<YEAR>_restart_<PE>.nc` ) so need to [[rebuild NEMO files]] first
2. Copy the files to your own data directory. I store restart files at `/home/d02/ssteinig/gc31/restarts/<SUITEID>` on [[NEXCS-MONSooN]].
