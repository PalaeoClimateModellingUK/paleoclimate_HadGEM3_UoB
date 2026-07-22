# 1.Check the ancillary files requiring change in the transition from piControl to LGM
## 1.1 UM
refer to `$UMDIR/ancil/data/ancil_versions/filenames/v9/ancils` for the default name of UM ancillary files
refer to `/home/n02/n02/an25872/work/ancils/ancils_kenji/um_ancils_PILGM4` (`/work/n02/n02/an25872/ancils/ancils_zikun/ancils_version/um_ancils_LGM2`) for the directory of UM ancillary.
- **Land sea mask**    
  export UM_ANCIL_MASK_FILE=qrparm.mask    
  export UM_ANCIL_MASK_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/land_sea_mask/etop01/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/land_sea_mask/etop01/LGM2) 

- **Land fractions**     
  export UM_ANCIL_LANDFRAC_FILE=qrparm.landfrac    
  export UM_ANCIL_MASK_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/land_sea_mask/etop01/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/land_sea_mask/etop01/LGM2)

- **Land-surface albedo**     
  export UM_ANCIL_GENLAND_FILE=qrclim.land   
  export UM_ANCIL_GENLAND_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/general_land/GlobAlbedo/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/general_land/GlobAlbedo/LGM)

- **Orography**    
  export UM_ANCIL_OROG_FILE=qrparm.orog
  export UM_ANCIL_OROG_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/orography/globe30/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/orography/globe30/LGM)

- **Sea-surface albedo (chlorophyll in seawater)**     
  export UM_ANCIL_GENSEA_FILE=qrclim.sea    
  export UM_ANCIL_GENSEA_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/general_sea/GlobColour/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/general_sea/GlobColour/LGM)

- **Large Scale Hydroglogy TOP-index (mean/sd/combined)**     
  export UM_ANCIL_LSH_TIND_FILE=qrparm.hydtop    
  export UM_ANCIL_LSH_TOPIND_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/hydrol_lsh/hydrosheds/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/hydrol_lsh/hydrosheds/LGM)
  
- **Soil temperature climatology**     
  export UM_ANCIL_SLT_FILE=qrclim.slt     
  export UM_ANCIL_SLT_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/soil_temp/amip/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/soil_temp/LGM)
  (initial state)     
  
- **Soil moisture and snow amount climatology**       
  export UM_ANCIL_SMOW_FILE=qrclim.smow     
  export UM_ANCIL_SMOW_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/smc_snow/gswp2_hwsd_vg/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/smc_snow/LGM)
  (initial state)    
  
- **Soil dust**    
  export UM_ANCIL_SOILDUST_FILE=qrparm.soil.dust    
  export UM_ANCIL_SOILDUST_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/soil_dust/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/soil_dust/LGM)

- **soil parameters**    
  export UM_ANCIL_SOIL_FILE=qrparm.soil    
  export UM_ANCIL_SOIL_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/soil_parameters/hwsd_vg/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/soil_parameters/LGM)

- **All surface single level emissions for sulphur cycle**    
  export UM_ANCIL_SULPSURF_FILE=qrclim.sulpsurf
  export UM_ANCIL_SULPDMS_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/classic_aerosol/LGM
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/classic_aerosol/LGM)
  (for classic aerosol)
  
- **TRIP River routing sequence**    
  export UM_ANCIL_TRIP_SEQ_FILE=qrparm.rivseq    
  export UM_ANCIL_TRIP_SEQ_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/rivers_trip/sequence/etopo5/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/rivers_trip/sequence/etopo5/LGM)    

- **TRIP River routing storage ancillary**     
  export UM_ANCIL_TRIP_STOR_FILE=qrclim.rivstor    
  export UM_ANCIL_TRIP_STOR_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/rivers_trip/storage/fekete/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/rivers_trip/storage/fekete/LGM) 
    
- **Vegetation fractions**
  export UM_ANCIL_VEGFRAC_FILE=qrparm.veg.frac    
  export UM_ANCIL_VEGFRAC_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/vegetation/fractions_igbp/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/vegetation/fractions_igbp/LGM)

- **Vegetation functional types**
  export UM_ANCIL_VEGFUNC_FILE=qrparm.veg.func    
  export UM_ANCIL_VEGFUNC_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_PI/vegetation/func_type_modis/PILGM4
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/mode_init/LGM)
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/vegetation/func_type_modis/LGM)

**UKCA-GLOMAP mode ancillaries (ancillary/netcdf format)**     
- **initial conditions for other UKCA fields**     
  export UM_ANCIL_MODEINIT_FILE=qrclim.modeinit_L85      
  export UM_ANCIL_MODEINIT_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/mode_init/LGM     
  (initial state; Not activated in Kenji's suite)


