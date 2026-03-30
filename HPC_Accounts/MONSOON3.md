## Quick introduction
MONSOON3 is another HPC that supporting the PAST2FUTURE projects.

### Official documentation about MONSOON3:
[What is MONSOON](https://code.metoffice.gov.uk/doc/monsoon3/whatIsMonsoon.html)     
[The job scheduler (PBS;Portable Batch System) on monsoon](https://code.metoffice.gov.uk/doc/monsoon3/pbs.html#scheduling-work-with-pbs)    
[MASS and MOOSE](https://code.metoffice.gov.uk/doc/monsoon3/firstHourOnMass.html#your-first-hour-on-mass)    
[use quota to know your amount limitation](https://code.metoffice.gov.uk/doc/monsoon3/file_systems.html#filesystems-datadir-and-quotas)
PBS, queue times and some advice
The TL;DR is that Monsoon is busy and current job queue times of 4+ hours is not unexpected. 
I hope the following is useful in explaining why  
 
### A quick introduction to PBS (copy from Veryan Horne)
 
PBS is an application that is, in effect, playing 3D Tetris on a conveyer belt. The Tetris pieces are everyone's jobs in the queue and the convey belt is time. It will attempt to place jobs in the most efficient manner, and the two things that have the most impact on how your job gets placed is the size of it (how many nodes you have requested) and how long those nodes have been requested for (wall-clock). A job that requests 50 nodes for a full 3 hours (aka 40% of Monsoon for the maximum amount of time) will sit in the queue for a long time as PBS waits for an appropriate gap to place it in.    
 
#### The size of Monsoon    
 
Monsoon has 125 compute nodes and 5 shared nodes. Therefore, what is a 'big job' can be roughly calculated as a % of the 125 and a % of the day. A 10 node job is 8% of the machine, and a full three hours out of twenty-four is 12.5% of the day. So this job is asking for ~10% of 'today', and if there are nine other similar sized jobs then the machine is 'full' for the day.     
 
#### How you can improve your job throughput    
 
Resize and/or reshape jobs. This may not always be possible, but if you are over-provisioning your jobs (asking for more nodes than it needs, and more time than it needs), reducing or reshaping it will likely be beneficial. For example, reshape a job asking for ten nodes for half an hour to five nodes for an hour.     
 
Split a larger job into smaller jobs. Like above, if you have a job that requests ten nodes for two hours, but the job can be split into two where one job requests three nodes for half an hour and the other seven nodes for an hour, throughput should be better.    
 
Offload jobs that do not require a full compute node to a shared node. You may find this particularly beneficial for archiving jobs and jobs that use MASS.

### Tips
#### Mule and iris on MONSOON3
`module load scitools`     
[The guidance for mule](https://metoffice.github.io/mule/mule/index.html)
 
