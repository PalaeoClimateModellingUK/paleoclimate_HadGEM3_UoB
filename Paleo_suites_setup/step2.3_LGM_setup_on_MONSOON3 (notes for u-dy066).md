## 3. From EOCENE to LGM on MONSOON3  
u-dy066 (coupling LGM suite) is copied from the Eocene suite (u-dv769)
### 3.1 ancil_list
#### 3.1.1 NEMO
##### 3.1.1.a domain_cfg.nc (configuration change)
Closed sea need some specific disposal in NEMO. For present day, the disposal is applied to Caspian sea.
In the LGM the black sea is also closed. for this reason, we need to apply the closed sea disposal also to the black sea.
It seems to be able to be set in the namelist of DOMAINcfg.

##### closea set in GC3 model
In GC3 model. The set of closed sea is controled by the switch `jp_cfg` at `nemo_cice > namelist > NEMO namelist > Domain > Configuration`.    
the `jp_cfg` is directly passed to `nemo/NEMOGCM/NEMO/OPA_SRC/DOM/closea.F90`.
```
 88       IF( cp_cfg == "orca" ) THEN
 89          !
 90          SELECT CASE ( jp_cfg )
 91          !                                           ! =======================
 92          CASE ( 1 )                                  ! ORCA_R1 configuration
 93             !                                        ! =======================
 94             ! This dirty section will be suppressed by simplification process:
 95             ! all this will come back in input files
 96             ! Currently these hard-wired indices relate to configuration with
 97             ! extend grid (jpjglo=332)
 98             isrow = 332 - jpjglo
 99             !
100             ncsnr(1)   = 1    ; ncstt(1)   = 0           ! Caspian Sea
101             ncsi1(1)   = 332  ; ncsj1(1)   = 243 - isrow
102             ncsi2(1)   = 344  ; ncsj2(1)   = 275 - isrow
103             ncsir(1,1) = 1    ; ncsjr(1,1) = 1
104             !
```
If you hope to add a closed sea, you will need to add some set over this codeblock.

##### closea set in GC5 model
Different with GC3, the bathymetry is no longer direct input of NEMO. In contrast, it becomes the file required by the generation of domain_cfg.nc. The new workflow requires using NEMO tool DOMAINcfg to read in bathymetry and output the domain_cfg.nc (see [the detailed procedure](https://github.com/PalaeoClimateModellingUK/paleoclimate_HadGEM3_UoB/blob/main/Paleo_suites_setup/step2.2_Eocene_setup_on_MONSOON3%20(notes%20for%20u-dv769).md#attemption-version-2-with-the-help-from-catherine-guiavarch-catherineguiavarchmetofficegovuk)).     
After that, we need to use a python script (make_closea_masks.py) to add the variable `closea_mask` to the domain_cfg.nc.     
The `make_closea_masks.py` is located at `/gws/pw/j25/past2future/users/zikunren/ancillary_generation/NEMO_grids`.




#### 3.1.2 UM
##### 3.1.2.a qrclim.veg.frac and (potential element)    
Don't have the permission to visit the following two pathes on ARCHER2.
```
/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/vegetation/fractions_igbp/PILGM4     
/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/vegetation/func_type_modis/PILGM4
```
Instead, we make use of the `qraclim.veg.frac` and `qrparm.veg.func` on the JASMIN located at:
```
/home/users/an25872/pmip4_vol2/users/kizumi/HadGEM3_boundary_data/projects/n96e_orca/n96e_orca1_go6/vegetation/fractions_igbp/
/home/users/an25872/pmip4_vol2/users/kizumi/HadGEM3_boundary_data/projects/n96e_orca/n96e_orca1_go6/vegetation/func_type_modis/
```
##### 2.1.2.b qrclim.smow and qrclim.slt (new ancil files)
These two files stores some soil-related fields: soil moisture, deep soil temperature and snow depth. In the suite now, these variables will be directly read from the restart dump. However, if you wanna spinup from the begining, it should be inserted into the suite, by : `um > namelist > Reconfiguration and ancillary control > Configure ancils`…

### 3.2 Model Physics
This suite is built based on the Eocene suite. Contrast with Eocene, LGM have some important ice processes. therefore, we should take on some switch.
#### 3.2.1 ice shelves.
switch on the `ln_isf` at `nemo > namelist > Surface Boundary Conditions (namsbc)`
#### 3.2.2 iceberg
switch on the `ln_icebergs` at `nemo > namelist > Surface Boundary Conditions (namsbc) > Iceberg settings (namberg)`
#### 3.2.3 orbital parameter(!!!!!!!in progressing)
Kenji set the orbital parameter by modifying the source codes, and I find a switch in the new version of UM to make it easier.    
[The tickes authored by Alison McLaren](https://code.metoffice.gov.uk/trac/um/wiki/ticket/7847/TicketDetails).     
`l_calc_orbprm_year` at `um > namelist > UM Science Settings > Shortwave`
But this seem to be only work for UM of `vn13.9`. 

#### 3.2.4 green house gases(!!!!!!!!!not changed yet)
