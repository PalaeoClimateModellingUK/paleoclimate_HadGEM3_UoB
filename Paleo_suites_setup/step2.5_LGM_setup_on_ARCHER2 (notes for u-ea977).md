## From EOCENE to LGM on MONSOON3  
u-ea977 (coupling LGM suite) is copied from the Eocene suite (u-ea539)
### 1 ancil_list
#### 1.1 NEMO
##### 1.1.a domain_cfg.nc (configuration change)
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




#### 1.2 UM
##### 1.2.a qrclim.veg.frac and (potential element)    

##### 1.2.b qrclim.smow and qrclim.slt (new ancil files)
These two files stores some soil-related fields: soil moisture, deep soil temperature and snow depth. In the suite now, these variables will be directly read from the restart dump. However, if you wanna spinup from the begining, it should be inserted into the suite, by : `um > namelist > Reconfiguration and ancillary control > Configure ancils`…

### 2 Model Physics
This suite is built based on the Eocene suite. Contrast with Eocene, LGM have some important ice processes. therefore, we should take on some switch.
#### 2.1 ice shelves.
off now, will be on in the future. In piControl, the influence of ice shelfves is treated as fixed runoff. In LGM, we might need to calculate it online.
#### 2.2 iceberg
off now, will be on in the future. In piControl, the influence of iceberg is treated as fixed runoff. In LGM, we might need to calculate it online.
#### 2.3 orbital parameter(!!!!!!!in progressing)
Kenji set the orbital parameter by modifying the source codes, and I find a switch in the new version of UM to make it easier.    
[The tickes authored by Alison McLaren](https://code.metoffice.gov.uk/trac/um/wiki/ticket/7847/TicketDetails).     
`l_calc_orbprm_year` at `um > namelist > UM Science Settings > Shortwave`
But this seem to be only work for UM of `vn13.9`.       
For this suite, we switch on `l_sec_var` and hard write the year to be 21000 years before present by `fcm co fcm:um.xm_tr@vn13.8` as `/home/n02/n02/an25872/FCM/UM/vn13.8_orb_LGM`. In `src/atmosphere/radiation_control/orbprm.F90` add three lines:
```
100
+++101  YEAR_LOCAL=-19000 ! hard-wire the year to be 21000 years before present
102
103 IF (lhook) CALL dr_hook(ModuleName//':'//RoutineName,zhook_in,zhook_handle)
104
105 ! The length of the calendar year may be set for a 360-day calendar
106 !  (as is often used in climate runs),
107 !  or for a real Gregorian calendar which has 365 days in
108 !  non-leap years and 366 in leap years.
109 IF (lcal360) THEN
110   diny=360.0
111 ELSE
112   ! Is this a leap year?
113   IF (MOD(year,4)    ==  0 .AND.                                               &
114      (MOD(year,400)  ==  0 .OR. MOD(year,100)  /=  0)) THEN
115     diny = 366.0
116     ! Is this a normal year?
117   ELSE
118     diny = 365.0
119   END IF
120 END IF
121
122 ! The orbital elements are normally set to default values, but
123 ! secular variations may be required in some longer climate runs.
124
125 IF (l_sec_var) THEN
126
+++127   year_offset = REAL( YEAR_LOCAL - year_ref )
128

```

#### 2.4 green house gases(!!!!!!!!!not changed yet)
#### 2.5 ln_zdftmx, ln_shlat2d, and ln_boost
Off now, refer to [step2.2](https://github.com/PalaeoClimateModellingUK/paleoclimate_HadGEM3_UoB/blob/8db2fc6870b03c4ac6ead3ce8699ad67c7442361/Paleo_suites_setup/step2.2_Eocene_setup_on_MONSOON3%20(notes%20for%20u-dv769).md#211d-tidal-mixing-at-ridges-k1-and-m2rowdrg) for further reason.

#### DEBUG
##### Seaice abnormally cumulated over the Arctica Ocean (first 10 years)
NEMO model crash suddenly, with no significant error in the output fields. 
**reason:**    
After closely checking the outputs of each timesteps (by adding the codes of below in the `app/nemo/file/file_def_nemo-oce.xml`), I found the `NaN` firstly appeared over the Arctica Ocean. Then I focus on this polar region, and find the sea ice thickness is abnormally increased with the model running. By checking the variables like sidmassth (sea-ice mass change from thermodynamics), sidmassgrowthbot (sea-ice mass change through basal growth), sst_m_bot (sea surface temperature), sitbot (temperature at the ice bottom), we find that over the area of seaice thickness increasing the SST is always about 0.3 degree colder than the ice bottom temperature. This should be the reason of continual ice growing. Then we further checked the temperature profile over this area. The results show that the temperature near the ocean bottom can reach -3 degree, which is inherited from the GC3.1 restart dump. With this unreal cold bottom water, the mixing causes a cooling effect to the SST, and maintains the difference bettween SST and sea ice bottom temperature, finally leads to the abnormal ice growing
Above all, in the GC3.1 LGM suite reach an spurious balance. So, the model can run with unphysical ocean profile. With the update to GC5, the sea ice distribution was initialized for the application of si3, therefore the balance is broken.
###### `app/nemo/file/file_def_nemo-oce.xml`     
```
 12    <file_definition type="one_file" split_freq="1ts" split_freq_format="%y%mo%d_%h%mi%s" name="@expname@_@freq@" mi    n_digits="4">
 13
 14       <file_group id="1ts" output_freq="1ts"  output_level="10" enabled=".TRUE.">
 15
 16         <file id="file7" name_suffix="_grid_D" >
 17           <field field_ref="ssh"          name="zos"       />
 18           <field field_ref="sst_con"      name="tos_con"    />
 19           <field field_ref="sss_abs"      name="sos_abs"    />
 20
 21         </file>
 22       </file_group>
 23    </file_definition>
```
**resolution:**    
switch on the initialization of NEMO.

##### Seaice abnormally cumulated over the Arctica Ocean (after 30years' running)
time series of Arctic on-site `sithic`(sea-ice thickness):    
<img width="1364" height="870" alt="e0141142-4583-4c58-8e4f-3c9582cd8fb6" src="https://github.com/user-attachments/assets/c1f92d4e-0b96-41f5-8727-df3f9aca3c0a" />
time series of Arctic on-site `sidmassgrowthwat`(sea-ice mass change through growth in supercooled open water(frazil)):    
<img width="1287" height="855" alt="ababf5198b4a9a3dda0bb1f034b04c03" src="https://github.com/user-attachments/assets/554786e5-9be2-4ef7-8f65-1253a5847328" />
By checking the sea-ice growth related variables, we found the main contributor to this abnormal sea-ice thickness is the **sidmassgrowthwat (ice-growth in supercooled open water)**, which happened over the fully ice-covered area. And the growth rate jumped at a tipping-point of about the 3rd decade. After a more careful investigation, we found this tipping-point is also the timepoint that `sitbot (temperature at the ice bottom)` and `sst_m_pot(sea surface temperature under the seaice)` Almost simultaneously reached -1.9 and got coupled again.
`sitbot (temperature at the ice bottom)`:    
<img width="990" height="609" alt="4ba9e7782c36bb481d07e4df647c8c79" src="https://github.com/user-attachments/assets/d3d41bdc-2bd5-433b-9d0b-607e51a87723" />
`sst_m_pot(sea surface temperature under the seaice)`:
<img width="992" height="609" alt="4920dd69942e31ea4377beeacaad8146" src="https://github.com/user-attachments/assets/b74df146-e866-438e-b156-517abe8f8c96" />    
**reason:**
To further uncover the cause of this abnormal sea-ice growth. We look into the source codes of NEMO-SI3.
And we found that the `sidmassgrowthwat` is basically controlled by two elements:
- `zdE: the specific enthalpy difference bettween seawater and forming ice [J/kg]`
- `qlead: heat balance of the lead (or of the open ocean)`
The mass of ice growth from frazil is calculated as the division of qlead by the specific enthalpy difference zdE. The resulting mass is then converted to ice volume using the ice density rhoi.
This provides a potential explanation for the persistent and abnormal sea-ice growth. As the sea-surface temperature approaches the local freezing point, the thermodynamic state of the seawater becomes increasingly close to that of the newly formed ice. Consequently, the specific enthalpy difference zdE between seawater and forming ice becomes small. Since the frazil-ice growth is calculated approximately as qlead / zdE, even a relatively small qlead can therefore produce a large sidmassgrowthwat. In other words, once the open water becomes sufficiently close to the freezing point, only a small amount of energy removal is required to generate a relatively large amount of frazil ice.     
This process is further enhanced by the treatment of sea-ice concentration in SI3. The ice fraction is capped at a maximum value of 0.997, leaving a small fraction of open water even when the grid cell is almost completely covered by ice. As a result, the frazil-ice growth term sidmassgrowthwat can continue to operate through the remaining open-water fraction, rather than being completely shut down by increasing ice concentration. More importantly, the resulting growth can occur without fully accounting for the strong insulating effect that would otherwise develop as the ice becomes increasingly thick. Therefore, a positive qlead combined with a small zdE can lead to continued ice growth, creating a positive feedback in which the ice thickness keeps increasing even after the surface temperature has reached the freezing point.
refer to [SI3_manual](https://zenodo.org/records/7534900) for more infomation.

**NEMO_4.0.4_GOSI9_package_16448_N216_GC5c/src/ICE/icethd_do.F90**   
```
278          ! --- Volume of new ice --- !
279          DO ji = 1, npti
280
281             zEi           = - ze_newice(ji) * r1_rhoi              ! specific enthalpy of forming ice [J/kg]
282
283             zEw           = rcp * ( t_bo_1d(ji) - rt0 )            ! specific enthalpy of seawater at t_bo_1d [    J/kg]
284                                                                    ! clem: we suppose we are already at the fre    ezing point (condition qlead<0 is satisfyied)
285
286             zdE           = zEi - zEw                              ! specific enthalpy difference [J/kg]
287
288             zfmdt         = - qlead_1d(ji) / zdE                   ! Fm.dt [kg/m2] (<0)
289                                                                    ! clem: we use qlead instead of zqld (icethd    ) because we suppose we are at the freezing point
290             zv_newice(ji) = - zfmdt * r1_rhoi
291
292             zQm           = zfmdt * zEw                            ! heat to the ocean >0 associated with mass     flux
293
294             ! Contribution to heat flux to the ocean [W.m-2], >0
295             hfx_thd_1d(ji) = hfx_thd_1d(ji) + zfmdt * zEw * r1_rdtice
296             ! Total heat flux used in this process [W.m-2]
297             hfx_opw_1d(ji) = hfx_opw_1d(ji) - zfmdt * zdE * r1_rdtice
298             ! mass flux
299             wfx_opw_1d(ji) = wfx_opw_1d(ji) - zv_newice(ji) * rhoi * r1_rdtice
300             ! salt flux
301             sfx_opw_1d(ji) = sfx_opw_1d(ji) - zv_newice(ji) * rhoi * zs_newice(ji) * r1_rdtice
302          END DO
```

**NEMO_4.0.4_GOSI9_package_16448_N216_GC5c/src/ICE/ice.F90**    
```
238    REAL(wp), PUBLIC, PARAMETER ::   epsi06 = 1.e-06_wp  !: small number
239    REAL(wp), PUBLIC, PARAMETER ::   epsi10 = 1.e-10_wp  !: small number
240    REAL(wp), PUBLIC, PARAMETER ::   epsi20 = 1.e-20_wp  !: small number

```

##### PPTRANSFER ONLY cover existing RUN_ID
When you pptransfer your data to JASMIN. There will be a directory established on the JASMIN.     
The name of this directory will be set as `$RUNID` but not the name of your workflow (for example u-ea977_test(ARCHER2) > u-ea977(JASMIN) ).     
**resolution:**
If you hope the JASMIN directory named as the name of your suite, you should change the definition of RUN_ID at flow.cylc. 
```
-204                RUNID = `echo -n $CYLC_WORKFLOW_NAME_BASE | cut -d - -f 2 | cut -c 1-5`
+204                RUNID = `echo -n $CYLC_WORKFLOW_NAME_BASE`
```

##### How to add undefault output variables in NEMO or SI3?
The outputs of NEMO or SI3 is controlled by a series of .xml files:
- context_nemo.xml: the trunk file, defines the `Field definition`, `file definition` and `grid definition`.
  in the work directory, it is copied from `source=$DIAG_XML_DIR/context_nemo-$DIAGNOSTICS.xml` as set in `app/nemo/rose-app.conf`
- field_def_nemo-oce.xml: defines the variables, which can be outputted.
  It is copied to the work directory from `$CYLC_SUITE_SHARE_DIR/fcm_make_ocean/build-ocean/etc/field_def_nemo-oce.xml`.
  And the `$CYLC_SUITE_SHARE_DIR/fcm_make_ocean/build-ocean/etc/field_def_nemo-oce.xml` is copied from `nemo/cfgs/SHARED/field_def_nemo-oce.xml`
- file_def_nemo-oce.xml: defines the files to be outputted, and the variables contained in each files.
  It is copied to work directory from the `app/nemo/file/file_def_nemo-oce.xml`.

if you want to add new output variables unclaimed in the source codes, there will be three locations you need to make some changes.
1. source codes:
2. field_def_nemo-oce.xml or field_def_nemo-ice.xml:
3. file_def
