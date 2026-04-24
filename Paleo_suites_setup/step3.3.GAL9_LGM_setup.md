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

### DEBUG
#### abrupt crash in one single timestep.
The NaN abruptly appears in the UKCA tracer at time timestep 880, then at the next timestep 881, the NaN spread to all the other fields like theta and U,V. Before this timepoint, the outputs seem to be quite normal.     
`dy662.fort6.pe00` at work/24010101T0000Z/atmos_main/pe_output
```
Maximum horizontal wind at timestep  880       Max wind this run
max_wind   level  proc         position        run max_wind level timestep
0.155E+03  85     55   43.1deg W     63.8deg N    0.167E+03   85   589
UKCA_TRACERS_COPY_FROM_UM: Copying tracers In.
tracer:      1    1 H2O2       =
0.1000000000495964E-11  0.1000000000495960E-11                     NaN
tracer:      2    2 DMS        =
0.8312278644748385E-32  0.8312278644748369E-32                     NaN
tracer:      3    3 SO2        =
0.3568510749850463-153  0.3568510749850446-153                     NaN
tracer:      4    4 H2SO4      =
0.2724964165074158E-37  0.2724964165074151E-37                     NaN
tracer:      5    5 DMSO       =
0.3369637675051805E-21  0.3369637675051791E-21                     NaN
tracer:      6    6 Monoterp   =
0.1312577604899065-304  0.1298114582365809-304                     NaN
tracer:      7    7 Sec_Org    =
0.1465393599975857-304  0.1447015886117305-304                     NaN
tracer:      8    8 Nuc_SOL_N  =
0.0000000000000000E+00  0.0000000000000000E+00                     NaN
tracer:      9    9 Nuc_SOL_SU =
0.0000000000000000E+00  0.0000000000000000E+00                     NaN
tracer:     10   10 Ait_SOL_N  =
0.0000000000000000E+00  0.0000000000000000E+00                     NaN
...

********************************************************************************************
Slow physics sources for ENDGame from atmos_physics1
min                      max                      average (non-bit reproducing  (1=has NaN 0= no NaN)
s_thetav :                             NaN                             NaN                             NaN          0.1000000000000000E+01
s_m_v    :         -0.9468092354301548E-04          0.8020940833108003E-04                             NaN          0.1000000000000000E+01
s_m_cl   :          0.0000000000000000E+00          0.1183353926335028E-04                             NaN          0.1000000000000000E+01
s_m_cf   :         -0.2627898078634533E-03          0.4870773701970376E-04                             NaN          0.1000000000000000E+01
s_m_rain :         -0.4527967343541150E-06          0.3383654032429559E-10                             NaN          0.1000000000000000E+01
********************************************************************************************
```
- **Reason：**
This situation is quiet similar to what we have encountered in the setup of piControl on MONSOON3. So I suspect the reason underlying the crash is the same as that one.     
On MONSOON3 there are two directories to store the ancillary files: `$UMDIR/ancil/atmos/GC5` and `/common/share/monsoon_ancils/atmos/GC5/`. Some files in the former one seem to have some flaw, which can lead to crash. So, to prevent potential error, we'd better use the ancillary under the second directory.
- **Resolution:**
switch `UM_ANCIL_DIR` at `app/install_ancil` from `$UMDIR/ancil/atmos/GC5` to `/common/share/monsoon_ancils/atmos/GC5/`.
