---
layout: post
title: "Solve your first Crackme"
categories: explained
tags: [under the hood, low level, C, programming, linux, security]
---

Hi everyone. this will be a quick tour in Labyrinth of linux directories

# / – The Root Directory

Everything on your Linux system is located under the / directory, known as the root directory the only user who has write privilege here is the root user. remember that / is different from /root as /root is the home directory of the root user

# /bin - Binaries

this contains binaries and executables that must be present when the system is mounted in single-user mode.Applications such as Firefox are stored in /usr/bin, while important system programs and utilities such as the bash shell are located in /bin

# /sbin - system binaries

same as definition of /bin but the binaries can only be used by root user or a high privilege user(sysadmin) we can find programs heres like for system maintenance purpose.

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










