# 1.Check the ancillary files requiring change in the transition from piControl to LGM
## 1.1 UM
refer to `$UMDIR/ancil/data/ancil_versions/filenames/v9/ancils` for the default name of UM ancillary files
refer to `/home/n02/n02/an25872/work/ancils/ancils_kenji/um_ancils_PILGM4` for the directory of UM ancillary.

### 1.1.1 surface
- **Land sea mask**    
  export UM_ANCIL_MASK_FILE=qrparm.mask    
  export UM_ANCIL_MASK_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/land_sea_mask/etop01/PILGM4    

- **Land fractions**     
  export UM_ANCIL_LANDFRAC_FILE=qrparm.landfrac    
  export UM_ANCIL_MASK_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/land_sea_mask/etop01/PILGM4

- **Land-surface albedo**     
  export UM_ANCIL_GENLAND_FILE=qrclim.land   
  export UM_ANCIL_GENLAND_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/general_land/GlobAlbedo/PILGM4

- **Sea-surface albedo (chlorophyll in seawater)**     
  export UM_ANCIL_GENSEA_FILE=qrclim.sea   
  export UM_ANCIL_GENSEA_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/general_sea/GlobColour/PILGM4

- **Large Scale Hydroglogy TOP-index (mean/sd/combined)**     
  export UM_ANCIL_LSH_TIND_FILE=qrparm.hydtop   
  export UM_ANCIL_LSH_TOPIND_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/hydrol_lsh/hydrosheds/PILGM4

- **Large Scale Hydroglogy TOP-index (mean/sd/combined)**     
  export UM_ANCIL_LSH_TIND_FILE=qrparm.hydtop   
  export UM_ANCIL_LSH_TOPIND_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/hydrol_lsh/hydrosheds/PILGM4

### 1.1.2 surface

### 1.1.3 UKCA-GLOMAP mode ancillaries (ancillary/netcdf format)
- **initial conditions for other UKCA fields**     
  export UM_ANCIL_MODEINIT_FILE=qrclim.modeinit_L85      
  export UM_ANCIL_MODEINIT_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/mode_init/LGM     
  (Not activated in Kenji's suite; it is usually read from restart dumps)

  

# UKCA-GLOMAP mode ancillaries (ancillary/netcdf format)
