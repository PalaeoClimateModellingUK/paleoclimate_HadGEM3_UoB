## setup the workflow for GC5_N96_ORCA1_ancils:
We check out the suite: `u-cc111/GC5_N96_ORCA1_ancils` as a startpoint of our setup.     
We note that the workflow is composed by twp group of tasks. One for `CAP`(the old workflow to generate ancillary files) and the other for `ANTS`(the new module). The running of ANTS require an python environment with ants packae installed. On the `spice`, it is realized by load module `ants`. In contrast, on archer2, the ants can only be aproached by [singularity](https://cms.ncas.ac.uk/miscellaneous/ants-container/). Therefore, the first step to setup the ancillary suite on archer2 should be apply the `singularity` to the `ANTS` workflow.
### python_env

