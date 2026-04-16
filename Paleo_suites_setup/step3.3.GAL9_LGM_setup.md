## start-point: **u-dy662**
I copied the AMIP suite **u-dy316** as **u-dy662**. Then make some changes on the suite **u-dy662**.

### 3.1 ancillary files:
#### 3.1.1 SST and seaice 
I remade and apllied the new SST ancillary files based on the SST from the [lgmDA reconstruction](https://github.com/jesstierney/lgmDA/tree/master).         
the scripts is stored on Machine Miocene:    
/home/an25872/work/ancils_generate/LGM/SST_and_seaice/regrid_interpolate*.py     
**warning:** the SST and seaice of lgmDA is built from the model outputs of CESM1.2. The LGM land-sea mask used by CESM1.2 is slightly different with HadGEM3. especially over the **Scotia Sea**. Therefore, after some processes like the regridding and masking, there is a unreal ice free over **Scotia Sea**. That should be keep in mind.     
In the future, we may fix it by some manual editting or try other data sources.

### 3.2 Model Physics
#### 3.2.1 orbital parameter
Kenji set the orbital parameter by modifying the source codes, and I find a switch in the new version of UM to make it easier.    
[The tickes authored by Alison McLaren](https://code.metoffice.gov.uk/trac/um/wiki/ticket/7847/TicketDetails).     
`l_calc_orbprm_year` at `um > namelist > UM Science Settings > Shortwave`
But this seem to be only work for UM of `vn13.9`. 

#### 3.2.2 green house gases
`co2_mmr` is set to 1.85e-04     
**Warning:** This is even not rightly set in GC3 suite `u-ds929`


