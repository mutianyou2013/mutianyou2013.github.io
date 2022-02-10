---
layout: post
read_time: true
show_date: true
title: "High Performance Clusters Usage Guide"
date: 2020-06-02
img: posts/20200602/titan.jpg
tags: [Tutorial, Working Environment]
category: theory
author: Tianyou Mou
description: "ELI5: what is a neural network."
---

New Server TinkerCliffs Quick Guide

1. Login in:

ssh -Y (yourID)@tinkercliffs1.arc.vt.edu 
or ssh -Y (yourID)@tinkercliffs2.arc.vt.edu 

For quick login, edit your local .bash_profile by: emacs ~/.bash_profile
Adding this part:
```python
alias tinkercliffs="ssh -Y (yourID)@tinkercliffs1.arc.vt.edu"
```

Then you can login by just type **tinkercliffs** locally.

2. After logging in, setup your home directory .bashrc file: 

```python
emacs /home/$(whoami)/.bashrc
```

Add this part to the end:
```python
if [ $SYSNAME == "tinkercliffs" ]

then
source /home/hxin/tinkercliffs/.bashrc

fi
```

3. For using VASP modules:

For using python2 and ase3.12, source:
```python
source /home/hxin/tinkercliffs/.modules.vasp-arc
source /home/hxin/tinkercliffs/.modules.vasp-grg
```
For using python3 and ase3.19, source:
```python
source /home/hxin/tinkercliffs/.modules.vasp-arc3
source /home/hxin/tinkercliffs/.modules.vasp-grg3
```
For using python3 and ase3.19 and vtsttools, source:
```python
source /home/hxin/tinkercliffs/.modules.vasp-vtst3
```

4. Preparing your .qsub file for submitting jobs:


Using vasp_sub.sh to submit Script.py jobs. Native vasp is still under testing.
For options of vasp_sub.sh, use: vasp_sub.sh -h 

Tinkercliffs has about 280 nodes in total and 128 cores per node, so for normal usage you can specify how many cores you want to use by -c option, and currently available allocations are DFT_XinLab and AmmoniaCat, you can specify it by -x option. Other options (-j, -n, -q, -s, etc.)are the same as other severs. 

Example: 
```python
vasp_sub.sh -j test -n 1 -w 1:00:00 -c 32 -q normal_q -x tcfriendly -s Script.py
```

5. Submitting and checking jobs:

Tinkercliffs uses original slurm system for jobs:
Submit your job:
```python
sbatch Script.py.qsub
```
Cancel your job:
```python
scancel JOBID 
```
Check your job list (cannot use qstat):
```python
squeue -u $(whoami) or squeue -u yourID
```
Check all jobs on queue:
```python
squeue
```

Check available idle nodes:
```python
sinfo -t idle
```

Check detail info of your specific job:
```python
scontrol show jobid JOBID
```

Some online references for you : [https://ubccr.freshdesk.com/support/solutions/articles/5000688140-submitting-a-slurm-job-script](https://ubccr.freshdesk.com/support/solutions/articles/5000688140-submitting-a-slurm-job-script)

You can also alias them to your familiar commands in .bashrc file.

6. Modules

Purge all modules:
```python
module purge
```

For tinkercliffs, all useful system tools and packages are setup by modules, so every time you make some changes to the modules and then you want to go back to the default settings using:
```python
module reset
```

Check your current modules:
```python
module list 
```

Check if the server has specific modules:
```python
module spider MODULENAME
```

Visualize geometries:

You can still use ase gui (or ag) to visualize you structures. Remember to source vasp modules with python3 and ase3.19. 

## Usage Update

1. Edit your .bashrc
 
For newriver: 
emacs -nw ~/.bashrc
 
source /work/common/hxin_lab/hxin/newriver/.bashrc
 
For cascades:
emacs -nw ~/.bashrc
 
source /work/common/hxin_lab/hxin/cascades/.bashrc
 
2. Source VASP
 
For newriver:
 
source /work/common/hxin_lab/hxin/newriver/.modules.vasp-arc3
source /work/common/hxin_lab/hxin/newriver/.modules.vasp-grg3
source /work/common/hxin_lab/hxin/newriver/.modules.vasp-vtst3
 
For cascades:
 
source /work/common/hxin_lab/hxin/cascades/.modules.vasp-arc3
source /work/common/hxin_lab/hxin/cascades/.modules.vasp-grg3
source /work/common/hxin_lab/hxin/cascades/.modules.vasp-vtst3
 
3. Source Quantum Espresso
 
For newriver:
 
source /work/common/hxin_lab/hxin/newriver/.modules.espresso6.5
 
For cascades:
 
source /work/common/hxin_lab/hxin/cascades/.modules.espresso6.5
 
4. New allocations
 
DFT_XinLab and hxin_lab
 
AmmoniaCat is not available anymore. 
 
5. Comments
 
Tinkercliffs can’t be used since there is no access to /work folder.
Only the python 3 version is available.
ASE 3.17 and ASE 3.19 can be loaded. 

