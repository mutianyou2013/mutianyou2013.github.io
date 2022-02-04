---
layout: post
read_time: true
show_date: true
title: "Bash tools"
date: 2021-02-01
img: posts/20210201/bash.png
tags: [neural networks, machine learning, artificial intelligence]
category: theory
author: Tianyou Mou
description: "ELI5: what is a neural network."
mathjax: yes
---

This is a tool to query the current running jobs in the server's queue. It has the function that you can directly obtain more info than the 'squeue' command, which is the popular command for the slurm workload manager. 

Key features:

- Show the RunTime and TimeLimit as the same time, which helps you to get your job status.
- Show the job's running directory, which can be directly accessed. It can save lots of time to find the job's directory when you are running multiple jobs

```bash
#!/usr/bin/env bash

if [ “$SYSNAME” = “newriver” ] || [ “$SYSNAME” = “blueridge” ] || [ “$SYSNAME” = “cascades” ]; then
JOBID=$(qstat | grep $(whoami)' ' | grep -Eo '[0-9]{6}')
elif [ “$SYSNAME” = “tinkercliffs” ]; then
JOBID=$(squeue -u $(whoami) | grep -Eo '[0-9]{6}')
elif [ “$SYSNAME” = “infer” ]; then
JOBID=$(squeue -u $(whoami) | grep -Eo '[0-9]{5}')
fi

RED='\033[0;31m'
NC='\033[0m'
BLUE='\e[34m'

QSUB=./Qsub.sh

NEWRIVERPATH=/work/common/hxin_lab/tianyou/Jobsub/newriver
NEWRIVERPATH1=/groups/hxin_lab/tianyou/Jobsub/newriver

SPACE=' '

if [ “$SYSNAME” = “newriver” ] || [ “$SYSNAME” = “blueridge” ]; then
for ID in $JOBID
  do
    CHECK=$(checkjob -v $ID) 
    JOBPATH1=$(echo $CHECK | grep -o -e /home.* -e /work.* -e /groups.* | cut -d ' ' -f 1)
    NEW=$(echo $JOBPATH1 | cut -d '/' -f -7)
    NEW1=$(echo $JOBPATH1 | cut -d '/' -f -8)
    QSTAT=$(qstat | grep $(whoami)' ' | grep $ID | cut -b 1-6,14,28-44,69-79,14)
    WALLTIME=$(echo $CHECK | grep -o WallTime.* | cut -b 10-33)
    if [ "$NEW" = "$NEWRIVERPATH" ]; then
    cd "$NEW1" 
    if test -f "$QSUB"; then
    REALPATH=$(cat Qsub.sh | grep -o -e /work.* -e /groups.*)
    echo -e $QSTAT${SPACE}${SPACE}$WALLTIME${SPACE}${RED}$REALPATH${NC}
    elif [ "$NEW" = "$NEWRIVERPATH" ]; then
    echo -e $QSTAT${SPACE}${SPACE}$WALLTIME${BLUE}' Not assigned yet.'${NC}
    else
    echo -e $QSTAT${SPACE}${SPACE}$WALLTIME${SPACE}${RED}$PWD${NC}
    fi
    else
    cd "$NEW"
    if test -f "$QSUB"; then
    REALPATH=$(cat Qsub.sh | grep -o /work.*)
    echo -e $QSTAT${SPACE}${SPACE}$WALLTIME${SPACE}${RED}$REALPATH${NC}
    elif [ "$(echo $JOBPATH1 | cut -d '/' -f -6)" = "$NEWRIVERPATH1" ]; then
    echo -e $QSTAT${SPACE}${SPACE}$WALLTIME${BLUE}' Not assigned yet.'${NC}
    else
    NP=$(echo $JOBPATH1 | rev | cut -d '/' -f 2- | rev)
    echo -e $QSTAT${SPACE}${SPACE}$WALLTIME${SPACE}${RED}$NP${NC}
    fi
    fi
  done
fi

CASCADESPATH=/groups/hxin_lab/tianyou/Jobsub/cascades

if [ “$SYSNAME” = “cascades” ]; then
for ID in $JOBID 
  do
    CHECK=$(scontrol show jobid $ID) # check job info
    JOBPATH1=$(echo $CHECK | grep -o -e /home.* -e /groups.* | cut -d ' ' -f 1) # check job path
    QSTAT=$(qstat | grep $(whoami)' ' | grep $ID | cut -b 1-6,7,15-30,62-72,7) # info from qstat
    WALLTIME=$(echo $CHECK | grep -o RunTime.* | cut -b 1-38) # wall time
    cd "$(echo $JOBPATH1 | cut -d '/' -f -6)"
    if test -f "$QSUB"; then # check if in the Jobsub folder
    REALPATH=$(cat Qsub.sh | grep -o -e /groups.* -e /work.*) # read real path from Qsub.sh file
    echo -e $QSTAT${SPACE}$WALLTIME${SPACE}${RED}$REALPATH${NC} 
    elif [ "$(echo $JOBPATH1 | cut -d '/' -f -6)" = "$CASCADESPATH" ]; then # if Qsub.sh not exist then not assigned yet.
    echo -e $QSTAT${SPACE}$WALLTIME${BLUE}' Not assigned yet.'${NC}
    else
    NP=$(echo $JOBPATH1 | rev | cut -d '/' -f 2- | rev) # if not in the Jobsub folder, then print real path read from job info
    echo -e $QSTAT${SPACE}$WALLTIME${SPACE}${RED}$NP${NC}
    fi
  done
fi

if [ “$SYSNAME” = “tinkercliffs” ] || [ “$SYSNAME” = “infer” ]; then
for ID in $JOBID
  do
    CHECK=$(scontrol show jobid $ID) # check job info
    JOBPATH1=$(echo $CHECK | grep -o -e /home.* -e /work.* -e /projects.* | cut -d ' ' -f 1) # check job path 
    QSTAT=$(squeue -u $(whoami)| grep $ID | cut -b 13-39,48-50) # info from qstat
    WALLTIME=$(echo $CHECK | grep -o RunTime.* | cut -b 1-38) # wall time
    cd "$(echo $JOBPATH1 | cut -d '/' -f -5)"
    if test -f "$QSUB"; then # check if in the Jobsub folder                                              
    REALPATH=$(cat Qsub.sh | grep -o -e /groups.* -e /work.*) # read real path from Qsub.sh file            
    echo -e $QSTAT${SPACE}$WALLTIME${SPACE}${RED}$REALPATH${NC}
    elif [ "$(echo $JOBPATH1 | cut -d '/' -f -6)" = "$CASCADESPATH" ]; then # if Qsub.sh not exist then not assigned yet.
    echo -e $QSTAT${SPACE}$WALLTIME${BLUE}' Not assigned yet.'${NC}
    else
    NP=$(echo $JOBPATH1 | rev | cut -d '/' -f 2- | rev) # if not in the Jobsub folder, then print real path read from job info                
    echo -e $QSTAT${SPACE}$WALLTIME${SPACE}${RED}$NP${NC}
    fi
  done
fi
```
This is a tool to query the current running jobs in the server's queue. It has the function that you can directly obtain more info than the 'squeue' command, which is the popular command for the slurm workload manager. 

Key features:

- Show the RunTime and TimeLimit as the same time, which helps you to get your job status.
- Show the job's running directory, which can be directly accessed. It can save lots of time to find the job's directory when you are running multiple jobs

```bash
if [ “$SYSNAME” = “newriver” ] || [ “$SYSNAME” = “blueridge” ]; then
tail -150 /work/common/hxin_lab/tianyou/Jobsub/newriver.txt > /work/common/hxin_lab/tianyou/Jobsub/newriver1.txt
cat /work/common/hxin_lab/tianyou/Jobsub/newriver1.txt
fi
if [ “$SYSNAME” = “cascades” ]; then
tail -150 /groups/hxin_lab/tianyou/Jobsub/cascades.txt > /groups/hxin_lab/tianyou/Jobsub/cascades1.txt
cat /groups/hxin_lab/tianyou/Jobsub/cascades.txt
fi
if [ “$SYSNAME” = “tinkercliffs” ]; then
tail -150 /home/tianyou/Jobsub/tinkercliffs.txt > /home/tianyou/Jobsub/tinkercliffs1.txt
cat /home/tianyou/Jobsub/tinkercliffs1.txt
fi
```
```bash
#!/usr/bin/env bash

RED='\033[0;31m'
NC='\033[0m'
BLUE='\e[34m'

QSUB=./Qsub.sh
STDE=./std.e
STDO=./std.o

PATHNAME=$PWD

while getopts  "h:q:" flag
do
  case $flag in
    h) echo "   -q - QUEUE is the queue to submit to (default is batch)"
       exit 0
     ;;
    q) if [ "$OPTARG" = "" ]; then
       JOBNAME=$(echo $PATHNAME | cut -d '/' -f 8)
       else
       JOBNAME=$(echo $OPTARG | cut -d '' -f 1)
       fi
       for JOB in $JOBNAME
        do 
	  if [ “$SYSNAME” = “newriver” ] || [ “$SYSNAME” = “blueridge” ]; then
	  cd /work/common/hxin_lab/tianyou/Jobsub/newriver/$JOB
	  elif [ “$SYSNAME” = “cascades” ]; then
	  cd /groups/hxin_lab/tianyou/Jobsub/cascades/$JOB
	  fi
	  echo -e ${RED}$PWD${NC}
	  if [ -e $QSUB ]; then rm ./Qsub.sh; fi
	  if [ -e $STDE ]; then rm ./std.e; fi
	  if [ -e $STDO ]; then rm ./std.o; fi
	  qsub Script.py.qsub
	done
     ;;
  esac 
done
shift $(($OPTIND - 1))
```
```bash
#!/usr/bin/env bash

rm ./Qsub.sh
rm ./std.*
qsub Script.py.qsub
```