### Issues on the schedule (update on 02/04/2026)
#### Plan A (setup GC5 for tuning):
- (1) GC5 PI :
    - official piControl.    
      - **status**: waiting for Met Office to port it to MONSOON3.

- (2) GC5 Eocene (u-dv769): The workflow to produce the ancillaries (in pariticular the missing files highlighted by Zikun)
    - The missing of ancillary files for GC5 Eocene.
      - **status**: waiting for Duncan's responce; attempt to run the model with old ancillary files;     
        Lists of modification:
        - neos: ln_EOS80 and ln_TEOS10
        - lateral diffusion: 3d to constant viscosity
        - calendar: 360day or Gregorian
        - rnf_coupling_method: 1d to 2d
        - remapping weights (coupling weights): GC3 or GC5
- (3) GC5 LGM (u-dy066)
    - <mark>the black sea high SSH (over 400meter).</mark>
      - **status**: 1. coupled task crash during the runstat check;
          2. the high SSH seems to be due to not applying the closed-sea disposal during the generation of NEMO grids.      
      - **to do**: learn more about the closed-sea disposal in domainCFG;     

      
#### Plan B (setup GC3 for tuning):
- (1) GC3 PI (on MONSOON3; u-dw345):
    - Dead Lock with MPI and `Error message: ADDR_CHK : Mis-match in start addresses` with one core UM
      - **to do**: switch the model outputs informaiton to the top-level;
                  try to rebuild and reconfigure all dependent modules directly on MONSOON3.
- (2) GC3 LGM (on archer2):
    - The coupling weights are required so that we can run the LGM correctly.    
      - **status**: coupling weight are being made in house by MO.
    - <mark>Try to reinitialize the LGM suite with Seb's initial profile.</mark>   
    - Zikun is trying to refine the domain decomposition and make it run more efficienctly.
      - **status**: 1152 cores are used for running on ARCHER2. (Speed: six model years per day)
      - **to do**: output more information by uplift the level of `$NLOGPRT`
      - 
#### Plan C (setup atmosphere-only suite for tuning):
- (1) GAL PI:
    - <mark>GA7.1 amip suite id</mark>
      - **to do**: find it by the rosie or Trac, or raise a question on the NCAS forum to ask for it.
    - <mark>the GA suite id for GC5 central tuning</mark>
      -**to do**: Contact john.rostron@metoffice.gov.uk to get the suite ID
    -  <mark>Read 'A clearer view of systematic errors in model development: two practical approaches using perturbed parameter ensembles'</mark>
                  
- (2) GAL LGM:
   - SST forcing collecting
      - **to do:** contact the people, who was working on the recontruction of LGM SST and seaice
      - **name list:** zhang feng(contact Xin Ren for email adress); Matt Osman; 
- (3) GAL EOCENE:
   - 

#### Others
- (1) The "slab/nudged" ocean.
    - setup paleo AMIP experiment.
      - **status**: we are looking for some fixed SST and sea ice distgribution, even the ice sheet.
      - **to do**: looking for collaboration with paleo data assimilation group.
    - spin-up acceleration.
      - **status**: `Anderson Acceleration` in developing.

### Done
- (1) GC5 PI:
    - The post-processing doesn't work
      - **results**: run successfully after switching off the postproc_integrity.
    - (MONSOON3 GC5-central) model explosion after five months' running
      - **results**: Zikun fixed it by renewing the ancils
    - (ARCHER2 GC5-unknown version) try to restart u-do332
      - **results**: Done

- (2) GC3 LGM (on archer2):
    - The coupling weights are required so that we can run the LGM correctly.    
      - **status**: mesh file need to be generated (a example for Eocene: /home/users/an25872/pmip4_vol2/users/ssteinig/gc31/ancils/Eocene/final_ancils_charlie_round2/ocean/bathy；    
      Kenji's resources for LGM:/home/users/an25872/pmip4_vol2/users/kizumi/HadGEM3_boundary_data/projects/peltier6g/N96_eORCA1_v2.2x)
      - **results**: Zikun generated it by using the DOMAINcfg tools.

- (3) GC5 Eocene (u-dv769): The workflow to produce the ancillaries (in pariticular the missing files highlighted by Zikun)
    - The missing of ancillary files for GC5 Eocene.
      - **status**: domain_cfg.nc is demanded to be generated.
      - **results**: Zikun generated it by using the DOMAINcfg tools.
    - The calendar mismatch.
      - **results**: The calendar of the Eocene suite is set to 360day now.
    - lib-4411 : UNRECOVERABLE library error:
      - **results**: resolution: [Keep {your rose suite}/app/coupled/file only contain one namcouple](https://github.com/PalaeoClimateModellingUK/paleoclimate_HadGEM3_UoB/blob/main/Paleo_suites_setup/step2.2_Eocene_setup_on_MONSOON3%20(notes%20for%20u-dv769).md#lib-4411--unrecoverable-library-error)



### Suites_Table for test:
| suite-id | remapping weight | lateral diffusion | neos |calendar | rnf_coupling_mod |---- |
| -------- | --------- | ---- | ---- | ---- |---- |---- |
| cn502    | GC5-PI | constat | ln_EOS80 | gregorian | 2d |
|          |           |                                                         |

### Optional (can be engaged in when blocked in P2F)
#### HadGEM3-GC3 work
- Based on the GC3.1, tune the model (relevant to P2F).
- Apply the ITCZ energetics framework on the outputs of HadGEM3-GC3.(Eocene piControl)
- MONSOON?
- ENSO?
#### DeepMIP work
- Help Seb with APRP
- ITCZ analysis
#### HadCM3
- Contribute to Dan's paper
- Orbital -CO2 coupling  
  - Eocene
  - Pheebe Ross's Thesis
    - Phoebe PhD thesis tuning HadCM3 + orbital ocean circulation: (Eocene):    
      https://spiral.imperial.ac.uk/entities/publication/694a0346-7cea-4acd-afec-31fb330c8f84
  - New simulation
  - ENSO
  - MONSOON
#### PV application in paleoclimate?
