## 3. From EOCENE to LGM on MONSOON3   
### 3.1 ancil_list
#### 3.1.1 NEMO
##### 3.1.1.a domain_cfg.nc (configuration change)
Closed sea need some specific disposal in NEMO. For present day, the disposal is applied to Caspian sea.
In the LGM the black sea is also closed. for this reason, we need to apply the closed sea disposal also to the black sea.
It seems to can be set in the namelist of DOMAINcfg. **Still in proceeding...**


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
#### 3.2.3 orbital parameter(!!!!!!!!!not changed yet)
#### 3.2.4 green house gases(!!!!!!!!!not changed yet)
