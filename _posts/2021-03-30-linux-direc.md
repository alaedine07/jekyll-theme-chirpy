---
layout: post
title: "linux directories explained"
categories: explained
tags: [under the hood, linux]
---

Hi everyone. this will be a quick tour in Labyrinth of linux directories

# / – The Root Directory

Everything on your Linux system is located under the / directory, known as the root directory the only user who has write privilege here is the root user. remember that / is different from /root as /root is the home directory of the root user

# /bin - Binaries

this contains binaries and executables that must be present when the system is mounted in single-user mode.Applications such as Firefox are stored in /usr/bin, while important system programs and utilities such as the bash shell are located in /bin

# /sbin - system binaries

same as definition of /bin but the binaries can only be used by root user or a high privilege user(sysadmin) we can find programs heres like for system maintenance purpose.

# /usr/share - shared data

It contains all the shared data used by the programs from /usr/bin, All default configuration files, themes, icons, wallpapers, sound files are stored here, one more folder I would like to mention here is the /usr/share/doc folder, where you can find the documentation files for programs installed on your system.

# /lib - Essential Shared Libraries

shared libraries used by the binaries when compiling. we can find the famous libc here, I already made a post about libraries

# /etc - Configuration Files

contains configuration files required by some programs, Configuration files control how the operating system or applications behave. this config files can be edited by hand(try to mess around).
for example we can find a famous config file called .bashrc which is is a Bash shell script that Bash runs whenever it is started interactively. It initializes an interactive shell session. You can put any command in that file that you could type at the command prompt.

# /boot - Static Boot Files

The /boot directory contains the files needed to boot the system – for example, the GRUB boot loader’s files and your Linux kernels are stored here. The boot loader’s configuration files aren’t located here, though – they’re in /etc with the other configuration files.

## /boot/vmlinuz

Normally the kernel or symbolic link to the kernel.

## /boot/grub

This subdirectory contains the GRUB configuration files including boot-up images and sounds

## /boot/grub/grub.cfg

The grub.cfg file contains Bash-like code and a list of installed kernels in an array ordered by sequence of installation.

# /home - Home folders

The /home directory contains a home folder for each user. For example, if your user name is bob, you have a home folder located at /home/bob. This home folder contains the user’s data files and user-specific configuration files. Each user only has write access to their own home folder and must obtain elevated permissions (become the root user) to modify other files on the system.

# /dev - Device files

Linux exposes devices as files, and the /dev directory contains a number of special files that represent devices. These are not actual files as we know them, but they appear as files – for example, ```/dev/sda``` represents the first SATA drive in the system. If you wanted to partition it, you could start a partition editor and tell it to edit /dev/sda.

This directory also contains pseudo-devices, which are virtual devices that don’t actually correspond to hardware. For example, ```/dev/random``` produces random numbers. /dev/null is a special device that produces no output and automatically discards all input – when you pipe the output of a command to ```/dev/null```, you discard it, some people call it the **black hole** of linux.

# /media - removable Media

The /media directory contains subdirectories where removable media devices inserted into the computer are mounted. For example, when you insert a CD into your Linux system, a directory will automatically be created inside the /media directory. You can access the contents of the CD inside this directory.

# /mnt - temporary mount points

Historically speaking, the /mnt directory is where system administrators mounted temporary file systems while using them. For example, if you’re mounting a Windows partition to perform some file recovery operations, you might mount it at /mnt/windows. However, you can mount other file systems anywhere on the system.

# /opt - Optional Packages

/opt is a directory for installing unbundled packages (i.e. packages not part of the Operating System distribution, but provided by an independent source), each one in its own subdirectory. They are already built whole packages provided by an independent third party software distributor.

# /lost+found – Recovered Files

You will have this directory if you use the ext4 file system. Most of the modern Linux distros use ext4, so most likely you have this folder. This is a file system specific folder that is used for data recovery in case of file corruption. Unless something bad has happened, this folder should be empty on your system. This /lost+found folder is produced on every separate partition. So, if your /home folder is on a separate partition, you should have this /lost+found folder in your home directory too.

# /proc – Kernel Files

This is a virtual file-system maintained by the Linux kernel. Usually, you do not touch anything in this folder. It is needed only for the kernel to run different processes.
The first thing that you will notice is that there are some familiar sounding files, and then a whole bunch of numbered directories. The numbered directories represent processes, better known as PIDs, and within them, a command that occupies them. The files contain system information such as memory (meminfo), CPU information (cpuinfo), and available filesystems. we will dive in this directory in another 
blog post.

# /var – Variable Files

The /var contains files that are of variable content, so their content is not static and it constantly changes. For example, this is where the log files are stored. If you don’t know, a log file is a file that records all events happening in your system while it is running. These log files often help to find out if something is not working correctly in your system.

if you want to be display every single file or directory in your system you can go to the root(not /root) directory and type the command
```tree```. i don't think it will take you long before pressing ctrl+c.

I hope you learned something from this post or triggered more curiosity about exploring linux :wink:  
