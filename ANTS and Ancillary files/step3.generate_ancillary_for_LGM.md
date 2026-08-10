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
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/vegetation/func_type_modis/LGM)
  
- **Ozone distribution**    
  export UM_ANCIL_OZONEL85O85=qrclim.ozone_L85_O85
  export UM_ANCIL_OZONE_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/ozone/sparc/1994-2005/LGM
  (/work/n02/n02/an25872/ancils/ancils_kenji/projects/n96e_orca/n96e_orca1_go6/ozone/sparc/1994-2005/LGM)

**UKCA-GLOMAP mode ancillaries (ancillary/netcdf format)**     
- **initial conditions for other UKCA fields**     
  export UM_ANCIL_MODEINIT_FILE=qrclim.modeinit_L85      
  export UM_ANCIL_MODEINIT_DIR=/work/n02/n02/kizumi/projects/n96e_orca/n96e_orca1_go6/mode_init/LGM     
  (initial state; Not activated in Kenji's suite)
## 1.2 NEMO
- **Grid configuration (domain_cfg)**
  source=/work/n02/n02/an25872/ancils/ancils_zikun/ancils_generate/GC5/domain_cfg_gen/LGM_ancil/LGMv2g/domain_cfg.nc
- **eddy_viscosity_3D**
  source=/work/n02/n02/an25872/ancils/ancils_zikun/ancils_generate/GC5/eddy_viscosity_3D/LGM_output_v2g/eddy_viscosity_3D.nc
- **mask_itf.nc**
  source=/work/n02/n02/an25872/ancils_additional/LGM/itf_mask/eORCA_R1_maskITF_v1.0_LGM.nc

  
# 2. How to generate these ancillary files?

With the help from Peter Hopcroft, We basically knows how the above UM ancillary files were generated for LGM.     
Firstly we will need a ancillary suite `u-cc111/GC5_N96_ORCA1_ancils`. In the [step2 under this session](https://github.com/PalaeoClimateModellingUK/paleoclimate_HadGEM3_UoB/blob/dd08748838655f68b352cd884e82e011bca601a8/ANTS%20and%20Ancillary%20files/step2.setup_an_ancillary_suite_on_archer2.md), we have successfully set up an runnable Ancillary suite (u-dz309) on ARCHER2. The next step to do so is transitioned the setup of it into a LGM one.

## 2.1 What to be changed in the UM ancillary suite (still in update and modification).
The main target of ancillary suite is to make a suite of consistent ancillary files based on the so-called master files (my understanding is the original files includes all the information about boundary conditions).     
According to the [previous work of Peter](https://link.springer.com/article/10.1007/s00382-014-2421-0), There is basically three kinds of master files demanding a transition to LGM:
- land-sea mask and topography:
  - changes to the orography, land sea mask and land ice as recon-structed
by Peltier (2004). `(Hopcroft et al., 2015)`
  -  The boundary conditions appropriate for 21 kyr BP are ice-sheet area, topography and sea-level from ICE-5G (Peltier, 2004) `(Hopcroft et al., 2023)`
  -  For coupling model:<img width="1744" height="588" alt="image" src="https://github.com/user-attachments/assets/d09671ca-db48-48ac-b423-eb6c264306d4" />
- Vegetation (dynamic vegetation for HadGEM2?)
- mineral dust:
  - For the LGM the source multiplier for mineral dust is expanded to cover new land gridcells and is set to zero over the Laurentide and Fennoscandian ice sheets. (Hopcroft et al., 2015)
  - In HadGEM2-ES the mineral dust cycle is coupled with the atmosphere and interactive vegetation (Bellouin et al., 2011; Woodward, 2011). Emissions are calculated as a function of dynamically determined bare soil area, soil moisture and wind speed. Emissions and atmospheric transport are calculated for six size bins which have radii of 0.0316–31.6 μm, with bin boundaries at 0.1, 0.316, 1.0, 3.16, and 10.0 μm. Dry and wet deposition are considered separately but direct dust-cloud interactions are not represented. Both pre-industrial and present-day simulations of mineral dust with HadGEM2-ES have been evaluated in previous studies (Bellouin et al., 2011; Fiedler et al., 2016; Hopcroft et al., 2015). (Hopcroft et al., 2023)
- river routing
- fossil fuel black and organic carbon (set to zero)
- topographic index field:
  - The topographic index field which determines the sub-grid hydrology was expanded to new land points at the LGM by deriving a logarithmic relationship between the topographic index mean and a measure of sub-grid orographic variability. (Hopcroft et al., 2023)
  - The topographic index ffeld which determines the sub-grid hydrology was expanded to new land points at the LGM by deriving a logarithmic relationship between the topographic index mean and a measure of sub-grid orographic variability. The latter was calculated at a resolution of 10 arc minutes using the global ETOPO1 data set (Armante and Eakins 2009). This orographic roughness ffeld was then regridded to the resolution of an existing topographic index ffeld (0.83° × 0.55°: N. Gedney, personal communication) in order to calculate the logarithmic relationship parameters used to derive the topographic index over new land points. The resultant globally resolved topographic index was ffnally regridded to the resolution required in HadGEM2 (1.875° × 1.25°). (Hopcroft et al., 2015)

Except for these ancils, the others are kept the same as piControl:
- aerosol emissions of sulphur dioxide, DMS, biogenic and biomass aerosols

## 2.2 How to build the 
