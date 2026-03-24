## 3. From EOCENE to LGM on MONSOON3   
### 3.1 ancil_list
#### 3.1.1 NEMO
#### 3.1.2 UM
##### 2.1.2.a  qrclim.veg.frac and (potential element)    
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
