- the experiments at different CO2 levels are each split into multiple separate suites/jobs due to changes in the model config (e.g. introduction of ozone remapping), spin-up acceleration (ocean heat removal/surgery) and even changing the HPC system from NEXCS/Monsoon to ARCHER2
- the 4xCO2 runs all use the reduced NEMO timestep to improve stability, this has been introduced to the x1, x2 and x3 runs during spin up 
- note that due to intermittent post-processing issues on ARCHER2 (e.g. writing of corrupt files on serial nodes) the individual spin-up jobs can have missing data for some years (normally < 5%)
- the production runs, i.e. the last 100 years+ are always complete without any missing data
- the intermittent suites with increased STASH (dd626, dd707, dd708, dd709) were used as a fallback in case we would not have gotten more ARCHER2 resources to have "publishable" results
## full history for each experiment

#### PI: 501 years (1851-2351)
| suite-id | years     | comment                                   |
| -------- | --------- | ----------------------------------------- |
| bq637    | 1851-2049 | control run from Charlie's Pliocene paper |
| cu413    | 2050-2119 | + interactive ozone scheme                |
| cz592    | 2118-2249 | switch from Monsoon to ARCHER2            |
| di629    | 2250-2351 | final stage with prod STASH               

#### 1xCO2: 1353 years (1850-3202)
| suite-id | years     | comment                                                 |
| -------- | --------- | ------------------------------------------------------- |
| cn502    | 1850-2268 | start from idealised T+S profiles                       |
| ct989    | 2267-2304 | reduced NEMO timestep for increased numerical stability |
| cu066    | 2297-2396 | + interactive ozone scheme                              |
| cz307    | 2396-2583 | switch from Monsoon to ARCHER2                          |
| dd626    | 2583-2682 | increased STASH (fallback)                              |
| de926    | 2683-3032 | minimum STASH to reduce computing costs                 |
| di622    | 3032-3202 | final stage with prod STASH                             |
|          |           |                                                         |
#### 2xCO2: 1463 years (1850-3312)
| suite-id | years     | comment                                                 |
| -------- | --------- | ------------------------------------------------------- |
| cn503    | 1850-2153 | start from idealised T+S profiles                       |
| ct536    | 2153-2222 | reduced NEMO timestep for increased numerical stability |
| cu067    | 2213-2395 | + interactive ozone scheme                              |
| cz577    | 2395-2727 | switch from Monsoon to ARCHER2 + ocean heat removed     |
| dd707    | 2727-2826 | increased STASH (fallback)                              |
| df055    | 2826-3148 | minimum STASH to reduce computing costs                 |
| di623    | 3148-3312 | final stage with prod STASH                             |
#### 3xCO2: 1409 years (1850-3258)
| suite-id | years     | comment                                                 |
| -------- | --------- | ------------------------------------------------------- |
| cn504    | 1850-2122 | start from idealised T+S profiles                       |
| ct550    | 2122-2191 | reduced NEMO timestep for increased numerical stability |
| cu070    | 2182-2355 | + interactive ozone scheme                              |
| cz578    | 2355-2704 | switch from Monsoon to ARCHER2 + ocean heat removed     |
| dd708    | 2704-2809 | increased STASH (fallback)                              |
| df056    | 2810-3130 | minimum STASH to reduce computing costs                 |
| di624    | 3131-3258 | final stage with prod STASH                             |
#### 4xCO2: 1170 years (1850-3019)
| suite-id | years     | comment                                             |
| -------- | --------- | --------------------------------------------------- |
| cn636    | 1850-1959 | start from idealised T+S profiles                   |
| cu072    | 1951-2129 | + interactive ozone scheme                          |
| cz590    | 2129-2488 | switch from Monsoon to ARCHER2 + ocean heat removed |
| dd709    | 2488-2582 | increased STASH (fallback)                          |
| df057    | 2582-2888 | minimum STASH to reduce computing costs             |
| di625    | 2888-3019 | final stage with prod STASH                         |

### final production runs
- simulations with increased [[production STASH]] for final years of all DeepMIP simulations
- production climatologies are based on final 100 years of monthly data of these runs
- full monthly mean data in netcdf format for all runs is available at archer2:/home/n02/n02/ssteinig/work/HadGEM3_output/
- raw model output for all fields is at jasmin:/home/users/ssteinig/pmip4_gws/ARCHER2_archive/

| suite id | description                                           | first sim year | last sim year | first clim year | last clim year |
| -------- | ----------------------------------------------------- | -------------- | ------------- | --------------- | -------------- |
| u-di629  | DeepMIP Eocene PI - final years - production STASH    | 2250           | 2351          | 2252            | 2351           |
| u-di622  | DeepMIP Eocene 1xCO2 - final years - production STASH | 3032           | 3202          | 3103            | 3202           |
| u-di623  | DeepMIP Eocene 2xCO2 - final years - production STASH | 3148           | 3312          | 3213            | 3312           |
| u-di624  | DeepMIP Eocene 3xCO2 - final years - production STASH | 3131           | 3258          | 3159            | 3258           |
| u-di625  | DeepMIP Eocene 4xCO2 - final years - production STASH | 2888           | 3019          | 2920            | 3019           |
