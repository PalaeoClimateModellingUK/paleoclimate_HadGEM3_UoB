# JASMIN (by CEDA: Centre for Environmental Data Analysis)
-  from their [website](https://www.ceda.ac.uk/services/jasmin/) : *"JASMIN is a globally unique data intensive supercomputer for environmental science. Over 160 science projects are currently supported, covering topics ranging from climate science and oceanography to air pollution, earthquake deformation and analysis of wildlife populations. JASMIN supports the data analysis requirements of the UK and European climate and earth system modelling community. It consists of multi-Petabyte fast storage co-located with data analysis computing facilities, with dedicated light paths to various key facilities and institutes within the community."*
-  we will transfer all model output from NEXCS/Monsoon to JASMIN (automatic post-processing that needs to be configured for each suite) to allow access/analysis/storage of large amounts of data
-  a very neat feature is the ability to use the [[JASMIN Notebook Service]] to interactively analyse the data in a web browser


## JASMIN basic procedure and Data transfer
### getting an account:

- instructions to get started are conveniently listed at https://help.jasmin.ac.uk/article/189-get-started-with-jasmin, but I think not all steps are essential. I did the following:
	1. [Create a JASMIN portal account](https://help.jasmin.ac.uk/article/4435-get-a-jasmin-account-portal).
	2. [Request ssh login access to JASMIN](https://help.jasmin.ac.uk/article/161-get-login-account)
	3. Apply for access to additional services and [group workspaces on JASMIN](https://help.jasmin.ac.uk/article/3801-apply-for-access-to-a-gws):  
		- **hpxfer** (use of high-performance data transfer services; don’t worry about supplying an IP address because NEXCS is whitelisted) 
		- **pmip4** (Paleoclimate Modelling Intercomparison Project PMIP4; access now managed by [[Charlie Williams]])
		- **nexcs** (workspace for interactions with Met Office NEXCS system; not sure if really necessary) 
		- **ncas_generic, nerctools, ncas-sci-M and ncas-climate** (analysis/post-processing tools)  
		- **nerctools** (gives access to NERC software tools.)

### [Home directory](https://help.jasmin.ac.uk/docs/getting-started/storage/?utm_source=chatgpt.com#home-directory)
Every JASMIN user is allocated a HOME directory located at `/home/users/<username>`. This directory is available across most of the interactive and batch computing resources, including the JASMIN login and transfer servers.    
Each home directory has a default quota of **100 GB**. Although you can’t directly check usage against your quota, you can find out the current size of your home directory as follows (the pdu command is a parallel variant of the du command, designed to work with the particular storage used for home directories on JASMIN).    
```
pdu -sh /home/users/<username>
```

### [Goup work space](https://help.jasmin.ac.uk/docs/short-term-project-storage/introduction-to-group-workspaces/)

[Manage a GWS](https://help.jasmin.ac.uk/docs/short-term-project-storage/managing-a-gws/#authorising-access-to-the-gws)
use `getent group | grep gws_past2future` to comfirm the list of users of GWS past2future.

[How to know the detail of a GWS](https://help.jasmin.ac.uk/docs/short-term-project-storage/managing-a-gws/#quota-resource-allocation-and-gws-lifetime)
```
pan_df -H /gws/pw/j07/workshop/
Filesystem             				Size   Used  Avail Use% Mounted on
panfs://panmanager03.jc.rl.ac.uk/gws/pw/j07     2.6T    16G   2.6T   1% /gws/pw/j07/workshop/
df -H  /gws/nopw/j04/ncas_generic
Filesystem                                      Size   Used  Avail Use% Mounted on
quobyte@sds.jc.rl.ac.uk/gws_ncas_generic        83T    80T   3.4T  96% /gws/nopw/j04/ncas_generic
```

### [sci server](https://help.jasmin.ac.uk/docs/interactive-computing/sci-servers/)
[How to connect to SCI servers](https://help.jasmin.ac.uk/docs/interactive-computing/login-servers/#connecting-to-a-sci-server-via-a-login-server)


### Use Globus to automatically transfer data from ARCHER2 to JASMIN ###
firstly, set a globus link as described below
(https://cms.ncas.ac.uk/unified-model/pptransfer-globus/)

secondly, to get through the task `fcm_make_pp_jasmin`, copy you ssh-key of JASMIN to the PUMA2. 
and set a config file as in the page [login-servers](https://help.jasmin.ac.uk/docs/interactive-computing/login-servers/)     
my config set:
```
Host login-*
  HostName %h.jasmin.ac.uk
  User an25872
  IdentityFile ~/.ssh/id_ecdsa_jasmin
  ForwardAgent yes

Host sci-vm-*
  HostName %h.jasmin.ac.uk
  ProxyJump login-02
  IdentityFile ~/.ssh/id_ecdsa_jasmin
```

[update to globus for pptransfer](https://code.metoffice.gov.uk/trac/roses-u/changeset/302861#file0)    

### DEBUG ###
task:fcm_make_pp_jasmin in GC5
```
  1 [FAIL] sci-vm-01:cylc-run/u-ds758/run9/share/fcm_make_pp_jasmin: cannot create mirror target
  2 [FAIL] ssh -n -oBatchMode=yes sci-vm-01 pwd # rc=255
  3 [FAIL] ssh: Could not resolve hostname sci-vm-01: Name or service not known^M
  4
  5 [FAIL] fcm make -f /home4/home/n02-puma/an25872/cylc-run/u-ds758/run9/work/19780901T0000    Z/fcm_make_pp_jasmin/fcm-make.cfg -C /home/n02/n02/an25872/cylc-run/u-ds758/run9/share/f    cm_make_pp_jasmin -j 4 mirror.target=sci-vm-01:cylc-run/u-ds758/run9/share/fcm_make_pp_j    asmin 'mirror.prop{config-file.name}=2' # return-code=2
  6 2025-10-30T16:59:21Z CRITICAL - failed/ERR
```
see [A error about ssh to svi machine of JASMIN](https://cms-helpdesk.ncas.ac.uk/t/could-not-resolve-hostname-jasmin/427/3)

## Interactive Computing on JASMIN

To request an interactive compute node on JASMIN, use `salloc` from a login or scientific analysis server.

### 1. Request an interactive compute node

For example, a short test can be run on the `debug` partition:

```bash
salloc -A past2future -p debug -q debug --cpus-per-task=4 --mem=16G
```

This requests:

* **Account:** `past2future`
* **Partition:** `debug`
* **QoS:** `debug`
* **CPUs:** 4
* **Memory:** 16 GB

The `debug` partition is intended for short interactive tests. **It is not necessary to use the `debug` partition for all interactive jobs.**

For example, for a longer calculation you could use another partition:

```bash
salloc -A past2future -p standard \
       --cpus-per-task=4 --mem=16G --time=02:00:00
```

The exact command depends on the partition and QoS available to your account.

---

### 2. Find a suitable partition

Use `sinfo` to see the currently available partitions:

```bash
sinfo
```

For example:

```text
PARTITION   AVAIL  TIMELIMIT  NODES  STATE
standard*   up     1-00:00:00 ...
special     up     2-00:00:00 ...
debug       up     1:00:00    ...
interactive up     3:00:00    ...
```

The main information to consider is:

* **PARTITION** — the name of the partition/queue.
* **TIMELIMIT** — the maximum running time allowed.
* **NODES** — the number of nodes available.
* **STATE** — the current state of the nodes, such as `idle`, `mix`, or `alloc`.

A more compact view can be obtained with:

```bash
sinfo -o "%P %a %l %D %t %N"
```

#### Typical choices

| Requirement                               | Possible choice     |
| ----------------------------------------- | ------------------- |
| Very short test/debugging                 | `debug`             |
| Interactive work for a few hours          | `interactive`       |
| General analysis / production calculation | `standard`          |
| Large-memory calculation                  | `special`           |
| GPU calculation                           | `orchid` / `gpumig` |

The available partitions and policies can change, so it is a good idea to check `sinfo` before submitting a job.

---

### 3. Check available QoS

A **partition** and a **QoS** are different things.

You can inspect the available QoS with:

```bash
sacctmgr show qos
```

You can also check the account associations for your user:

```bash
sacctmgr show assoc user=$USER
```

If you are unsure whether a particular QoS can be used with your account, check the JASMIN documentation or try submitting a small interactive job.

If the default QoS is appropriate, you may not need to specify `-q` explicitly:

```bash
salloc -A past2future -p standard \
       --cpus-per-task=4 --mem=16G --time=02:00:00
```

---

### 4. Wait for the allocation

Slurm may initially report:

```text
salloc: Pending job allocation XXXXXXXX
salloc: job XXXXXXXX queued and waiting for resources
```

This does **not necessarily indicate an error**. It means that Slurm is waiting for suitable resources.

Once the resources are available, you should see something like:

```text
salloc: Granted job allocation XXXXXXXX
salloc: Nodes host1001 are ready for job
```

You can then check the allocated compute node:

```bash
hostname
```

and the allocated CPUs:

```bash
echo $SLURM_CPUS_PER_TASK
```

---

### 5. Run the analysis interactively

Once the compute node has been allocated, you can run your analysis directly:

```bash
python volume_mean_T_S.py
```

You can also measure the execution time:

```bash
time python volume_mean_T_S.py
```

This is useful for testing whether the calculation is limited by CPU, memory, or file I/O.

---

### 6. Release the compute node

When you have finished, release the interactive allocation:

```bash
exit
```

Alternatively:

```bash
scancel $SLURM_JOB_ID
```

It is important to release the allocation when you are finished so that the resources become available to other users.

---

### 7. Recommended workflow

A useful workflow is:

```text
Check available resources
        ↓
      sinfo
        ↓
Choose an appropriate partition
        ↓
      salloc
        ↓
Test the analysis interactively
        ↓
Measure performance with `time`
        ↓
If successful → submit a batch job with `sbatch`
```

For a small NetCDF analysis such as calculating global ocean mean temperature and salinity, `debug` is useful for **initial testing**. If the calculation takes longer than the debug time limit, use a longer-running partition such as `standard` instead.





## Data Analysis on JASMIN

### [Python on JASMIN](https://help.jasmin.ac.uk/docs/software-on-jasmin/conda-environments-and-python-virtual-environments/) 
module load jaspy

### [mule on JASMIN](https://help.jasmin.ac.uk/docs/software-on-jasmin/conda-environments-and-python-virtual-environments/) 
module load ants


### link stash codes with variable name

download the STASH CODE TABLE here(https://reference.metoffice.gov.uk/um/release/_9.1)
https://reference.metoffice.gov.uk/um/stash/_<stash_code_you_want_to_know>
