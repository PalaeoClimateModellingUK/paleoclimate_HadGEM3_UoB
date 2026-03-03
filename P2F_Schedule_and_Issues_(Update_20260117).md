### Issues on the schedule (update on 18/02/2026)
- (1) GC3 PI (on MONSOON3; u-dw345):
    - Dead Lock with MPI and `Error message: ADDR_CHK : Mis-match in start addresses` with one core UM
      - **to do**: switch the model outputs informaiton to the top-level;
                  try to rebuild and reconfigure all dependent modules directly on MONSOON3.
- (2) GC3 LGM (on archer2):
    - The coupling weights are required so that we can run the LGM correctly.    
      - **status**: coupling weight are being made in house by MO.
    - Zikun is trying to refine the domain decomposition and make it run more efficienctly.
      - **status**: 1152 cores are used for running on ARCHER2. (Speed: six model years per day)
      - **to do**: output more information by uplift the level of `$NLOGPRT`
        
- (3) GC5 PI (u-dv344):
    - (ARCHER2 GC5-unknown version) try to restart u-do332
      - **status**: in processing
      - **to do**: use it as a start point of the Eocene suite setup.
- (4) GC5 Eocene (u-dv769): The workflow to produce the ancillaries (in pariticular the missing files highlighted by Zikun)
    - The missing of ancillary files for GC5 Eocene.
      - **status**: waiting for Duncan's responce; attempt to run the model with old ancillary files;     
        Lists of modification:
        - neos: ln_EOS80 and ln_TEOS10
        - lateral diffusion: 3d to constant viscosity
        - calendar: 360day or Gregorian
        - rnf_coupling_method: 1d to 2d
        - remapping weights (coupling weights): GC3 or GC5
    - The calendar mismatch.
      - **status**: The calendar of the PI suite is gregorian, but the restart file from GC3.1 suite and some existing ancillary files are 360-day. This mismatch leads to error.
      - **to do**
    - lib-4411 : UNRECOVERABLE library error:
      - **status**: The same configurations but one runs(u-dv344) successfully and the other(u-dv769_med_dv344) shows error as the title.
      - **plan**(in processing): copy the files in u-dv769_med_dv344 to the u-dv344 (make the u-dv344_cp_dv769_med then run it as `u-dv344`), see what will happen.
- (5) The "slab/nudged" ocean.
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
- (2) GC3 LGM (on archer2):
    - The coupling weights are required so that we can run the LGM correctly.    
      - **status**: mesh file need to be generated (a example for Eocene: /home/users/an25872/pmip4_vol2/users/ssteinig/gc31/ancils/Eocene/final_ancils_charlie_round2/ocean/bathy；    
      Kenji's resources for LGM:/home/users/an25872/pmip4_vol2/users/kizumi/HadGEM3_boundary_data/projects/peltier6g/N96_eORCA1_v2.2x)
      - **results**: Zikun generated it by using the DOMAINcfg tools.
- (3) GC5 Eocene (u-dv769): The workflow to produce the ancillaries (in pariticular the missing files highlighted by Zikun)
    - The missing of ancillary files for GC5 Eocene.
      - **status**: domain_cfg.nc is demanded to be generated.
      - **results**: Zikun generated it by using the DOMAINcfg tools.


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
