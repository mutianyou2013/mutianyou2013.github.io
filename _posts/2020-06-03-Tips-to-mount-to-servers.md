---
layout: post
read_time: true
show_date: true
title: "Tips to Mount Remote directories"
date: 2020-06-03
img: posts/20200603/sshfs.png
tags: [Tutorial, Working Environment]
author: Tianyou Mou
description: "Use SSHFS to mont remote file systems."
---

## Mount a local directory to servers

For a MacOS user: 
Go to [https://osxfuse.github.io](https://osxfuse.github.io) and download FUSE for MacOS and SSHFS softwares (you can download the latest versions of them) and install both following the instructions.

<center><img src="./assets/img/posts/20200603/Fuse.png" width="540"></center>

After installation, go to terminal and 3 steps to setup your folder:
```python
cd ~
mkdir newriver
mkdir newriver_home
```

## Tip 1: Using alias for quick usage

Open ~/.bash_profile, add two mount commands:

```python
alias mount_newriver="sshfs #yourid#@newriver1.arc.vt.edu:/work/common/hxin_lab/#yourid#  /Users/#yourmacusername#/newriver”

alias mount_newriver_home="sshfs #yourid#@newriver1.arc.vt.edu:/home/#yourid# /Users/#yourmacusername#/newriver_home"
```

Save your .bash_profile. 

To use Mount commands:

- mount_newriver: To mount your work folder to server(/work/common/hxin_lab/#yourid#), you can edit this folder locally and the work folder on the server can also be updated. Since the server doesn’t support any visualization interfaces, using this mount command you can use ASE or material studios to visualize the structure of your system. 
- mount_newriver_home: same command as mount_newriver, but will mount your home folder(/home/#yourid#). Other operations are the same as mount_newriver.
- umount_newriver and umount_newriver_home: every time you want to disconnect your mount from the server you can type those commands in your terminal.


## Tip 2: Prevent losing connection

When your computer is asleep, you will lose connection to the server. Simple way to reconnect to it needs 3 steps: 
- Close all terminal windows and then right click the terminal app and then quit the app.
- Reopen terminal and type: umount newriver
- Then type: mount_newriver

