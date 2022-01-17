---
layout: post
read_time: true
show_date: true
title: "Setting up your working environment"
date: 2021-04-02
img: posts/20210402/post7-header.webp
tags: [neural networks, machine learning, artificial intelligence]
category: theory
author: Armando Maynez
description: "ELI5: what is a neural network."
---
  
# Working-environment

## How to log onto the ARC computing system

## VASP Compilation

For this instruction, make sure you copy the right files from the right directory. **‘/path/to’** means you have to replace it with the specific path in your own account. 

1.**[./]** First generate vasp folder as the root folder: 
```python
mkdir vasp_suite
```
For tinkercliffs, you can make it in the $WORK directory: 
```python
cd $WORK; mkdir vasp_suite
```
For a more systematic way, you can put vasp suite into the usr directory by: 
```python
cd $WORK; mkdir usr; cd usr; mkdir vasp_suite
```
Or compile it in the /home directory which can be used for all three servers.

2.**[./vasp_suite]** Generate vasp lib folder to save pseudopotential files: 
```python
mkdir lib
```
3.**[./vasp_suite/lib]** Download pseudopotential files you need, for instance: potpaw_LDA.52.tar.gz and potpaw_PBE.52.tar.gz. This is for the ase interface binary. 

You can go to this folder to copy the pseudopotential source package: 
```python
/work/common/hxin_lab/hxin/Installation
```
For tinkercliffs, you can firstly copy and paste everything to your home directory:
Login cascades, and then: 
```python
cp -r /work/common/hxin_lab/hxin/Installation ~
```
Then next time when you are on the tinkercliffs, you can copy from:
```python
cp ~/Installation /path/to/where/you/want
```
4.**[./vasp_suite/lib]** Tar files: 
```python
tar -xvf potpaw_LDA.52.tar.gz ; tar -xvf potpaw_PBE.52.tar.gz
```
More info about tar command: 
https://www.freecodecamp.org/news/tar-in-linux-example-tar-gz-tar-file-and-tar-directory-and-tar-compress-commands/

This command will tar your .gz files and generate folders saving pseudo potentials

5.**[./vasp_suite]** Go back to the vasp_suite root folder and make another source folder: 
```python
mkdir src
```
6.**[./vasp_suite/src]** Go to src folder and download the vasp package: vasp.5.4.4.tar.gz

You can go to this folder to copy the vasp source package: 
```python
/work/common/hxin_lab/hxin/Installation
```
7.**[./vasp_suite/src]** Same way to tar this .gz file: 
```python
tar -xvf vasp.5.4.4.tar.gz
```
8.**[./vasp_suite/src/vasp.5.4.4]** Then go to the generated vasp.5.4.4 folder and such folders and files are in this folder:
```python
arc bin build makefile README src
```
9.**[./vasp_suite/src/vasp.5.4.4]** Copy one of the makefile.include.arch files in **./arch** to **./makefile.include**. Take one that most closely reflects your system (hopefully). For instance, on a linux box with the Intel Composer suite: 
```python
cp arch/makefile.include.linux_intel  ./makefile.include
```
You can go to this folder to copy the makefile I’ve made: 
```python
/work/common/hxin_lab/hxin/Installation
```
For the tinkercliffs server, use this one instead: 
```python
/work/common/hxin_lab/hxin/Installation/tinkercliffs
```

10.**[./vasp_suite/src/vasp.5.4.4]** So right now we have this makefile.include file and then we need to set it up. 

11.**[./vasp_suite/src/vasp.5.4.4/]** Add beef to vasp. Copy and paste the beef folder:
```python
cp -r /work/common/hxin_lab/hxin/Installation/beef ./
```
Change BEEF tag in the makefile.include file to :
```python
BEEF       = -L/path/to/vasp_suite/src/vasp.5.4.4/beef/lib -lbeef
```

Save and exit the makefile.include file.

12.**[./vasp_suite/src/vasp.5.4.4/src]** After setting up the makefile, for vtsttools installation, another several steps need to be done(No need if you don’t use VTST tools). Go to src folder under vasp.5.4.4:
```python
cd ./vasp_suite/src/vasp.5.4.4/src
```

13.**[./vasp_suite/src/vasp.5.4.4/src]** In this folder, edit main.F file to find and replace (using **ctrl+s** for quick search):
```python
CALL CHAIN_FORCE(T_INFO%NIONS,DYN%POSION,TOTEN,TIFOR, &
     LATT_CUR%A,LATT_CUR%B,IO%IU6)
```
with
```python
CALL CHAIN_FORCE(T_INFO%NIONS,DYN%POSION,TOTEN,TIFOR, &
     TSIF,LATT_CUR%A,LATT_CUR%B,IO%IU6)
```
14.**[./vasp_suite/src/]** Backup your chain.F since vtsttools also has one. Go back to the upper folder and make a backup folder. 
```python
cd ./vasp_suite/src/ ; mkdir backup ; cd backup 
cp ./vasp_suite/src/vasp.5.4.4/src/chain.F ./
```
15.**[./vasp_suite/src/]** Download source codes of vtsttools by this command (using svn checkout):
```python
svn co http://theory.cm.utexas.edu/svn/vtsttools 
```
Or directly copy from directory: 
```python
cp -r /work/common/hxin_lab/hxin/Installation/vtsttools ./
```
A vtsttools folder will be generated and contains 3 subfolders:
```python
docs vtstcodes vtsttools
```
16.**[./vasp_suite/src/vasp.5.4.4/src]** Go to **vasp.5.4.4/src** folder, copy and paste all source codes from vtsttools which are inside of vtstcode folder:
```python
cd ./vasp_suite/src/vasp.5.4.4/src
cp ./vasp_suite/src/vtsttools/vtstcode/* ./
```
17.**[./vasp_suite/src/vasp.5.4.4/src]** At the same folder, edit .objects file to change vasp source codes. Better way is to directly copy from 
```python
/work/common/hxin_lab/hxin/Installation/tinkercliffs/src/.objects
```
**emacs .objects** (or any tools available to you to edit the file)
add the following objects before chain.o:
```python
bfgs.o \
dynmat.o  \
instanton.o  \
lbfgs.o \
sd.o  \
cg.o \
dimer.o \
bbm.o \
fire.o \
lanczos.o \
neb.o  \
qm.o \
opt.o \
```
Make sure you delete all spaces after  **‘\’** and then press **‘Enter’**. 
Save and exit the .objects file.

18.**[./vasp_suite/src]** Download VASPsol from github for solvation calculations.
```python
git clone https://github.com/henniggroup/VASPsol.git
```
19.**[./vasp_suite/src]** Change solvation source code:
```python
cp ./VASPsol/src/solvation.F ./vasp.5.4.4/src/
```
20.**[./vasp_suite/src/vasp.5.4.4]** Make sure you add **-Dsol_compat** option to the list of precompiler options (**CPP_OPTIONS**) in the makefile.include

![](images/Picture1.png)

For the makefile.include file copied directly from the Installation folder, you don’t need to make any changes. 

21.**[./vasp_suite/src/vasp.5.4.4/]** The last step is the actual compilation of VASP:

On newriver:
```python
module purge; module load intel/15.3; module load openmpi/1.8.5; module load mkl
```
On cascades:
```python
module purge; module load intel/15.3; module load openmpi/2.0.0; module load mkl
```
On tinkercliffs:
```python
module load VASP
```
Then:
```python
make std gam ncl
```
If you are encountering any errors, go to step 24 for some possible solutions.

After successful compilation, three binaries of vasp can be found in **“./vasp_suite/src/vasp.5.4.4/bin"**, namely the standard full k-point version **"vasp_std"**, the gamma-only version **"vasp_gam"** and the non-collinear version **"vasp_ncl"**.

22.Running vasp in parallel with, say 4 MPI ranks, is done using
```python
mpirun -np 4 /path/to/vasp_std
```
Remember to set up all dependencies before running VASP.

23.**[./vasp_suite/kernel]** Make another directory for the vdw kernel.
```python
cd ./vasp_suite; mkdir kernel
cp /work/common/hxin_lab/hxin/Installation/kernel/vdw_kernel.bindat ./ 
```
24.Potential Errors:

(a).If you get some errors like the screenshot below, you probably didn’t delete all spaces in step 17. Better to directly copy and paste the right one.

![](images/Picture2.png)

(b). If you get some errors like the screenshot below, you’ll probably need to set up your locale:

Edit your local bash_profile by opening a new terminal window and type: emacs ~/.bash_profile
Add the following two lines to it and save. 
```python
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

![](images/Picture3.png)

For more installation details check out the websites listed below.

https://www.vasp.at/wiki/index.php/A_CentOS_based_installation_of_VASP

https://cms.mpi.univie.ac.at/vasp/vasp/vasp.html

https://theory.cm.utexas.edu/vtsttools/installation.html

https://github.com/henniggroup/VASPsol

