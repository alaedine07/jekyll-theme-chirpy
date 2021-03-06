---
layout: post
title: "What happens when you type gcc main.c ?"
categories: explained
tags: [under the hood, low level, C, programming, linux]
---

Hi everyone. In this blog post I will be explaining what happens when you compile your C program with gcc .

## What is Gcc ?

First GCC is the GNU COMPILER COLLECTION it’s a set of compilers who can support different programming languages and a lot of architectures . Mainly used in linux system, It was used first to compile C language which was used to write the linux kernel .
generating an executable binary from a C source code with gcc happens in 4 steps: preprocessing/compilation/assemblying/linking

## Preprocessing

This is the first step, a preprocessor is a program that process input data to produce an output that will be used as input again. so in this step the preprocessor will take the source code and generate another output to be used for the compiling step
![](/assets/img/gcc-main/1.png)

a preprocessor will take care of the stripping of comments,macro substitution,expansion of the included files from libraries . a lot of other things get done but it’s not the main purpose of this post

## Compilation

Even if C is considered a low level language . well in a certain era C was considered high level because people were coding in Assembly . compiling the code will turn the C code into assembly instructions which is the closest thing to machine language .
![](/assets/img/gcc-main/2.png)

the assembly here is using AT&T syntax . we can still notice the message “hello world” and the call to puts .

## assemblying

this step will converts the assembly instructions into the object code. The object code file contains a sequence of machine-readable instructions that is processed by the CPU in a computer.
![](/assets/img/gcc-main/3.png)

as I told you those are machine instructions so they are not ASCII readable. the few strings you can read here are data related to the ELF file .

## linking

after object code has been developed, the linker generates the executable file It links all the function calls with their original definition. for example, if we used the printf function. The printf function is a standard function that is provided by the C compiler that your current object file knows nothing about. In order to use this function, we need to use the linker in order to link our program with the precompiled libraries provided to us by the C compiler(stdio.h for example).
![](/assets/img/gcc-main/4.png)

## Resume

all this magic happens when you type gcc . I hope this is clear now and still there is a lot of terms used here that we need to explore more to understand deeply how computers works.
![](/assets/img/gcc-main/final.png)
