## start-point: **u-dy662**
I copied the AMIP suite **u-dy316** as **u-dy662**. Then make some changes on the suite **u-dy662**.

### 3.2 Model Physics
#### 3.2.3 orbital parameter
Kenji set the orbital parameter by modifying the source codes, and I find a switch in the new version of UM to make it easier.    
[The tickes authored by Alison McLaren](https://code.metoffice.gov.uk/trac/um/wiki/ticket/7847/TicketDetails).     
`l_calc_orbprm_year` at `um > namelist > UM Science Settings > Shortwave`
But this seem to be only work for UM of `vn13.9`. 

#### 3.2.4 green house gases
`co2_mmr` is set to 1.85e-04
Warning: This is even not rightly set in GC3 suite `u-ds929`
