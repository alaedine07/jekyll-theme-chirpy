---
layout: post
title: "C static libraries . Definition and how to use them"
categories: explained
tags: [under the hood, low level, C, programming, linux]
---

Hello guys, in this Post I will explain what are libraries, why we use them and how to create a custom one.

## what is a library

Libraries consist of a set of related functions to perform a common task. for example, the standard C library, libc.a, is automatically linked into your programs by the “gcc” compiler . Standard system libraries are usually found in /lib and /usr/lib/ directories. Check out those directories.

By default the gcc compiler or more specifically its linker needs to be directed to which libraries to search other than the standard C library — which is included by default.

Also a library is a compiled binary file (or in general, a collection of binary files), which can be linked when compiling a program to take advantage of the functions made available by the library. Header files are then used to identify the names and the signatures of those functions, so that the compiler knows how to call them.

A library filename always starts with lib. The last part of the name determines what type of library it is:

**.a**: static, traditional libraries. Applications link to these libraries of object code.

**.so**: dynamically linked shared object libraries. These libraries can either be linked in at runtime but statically aware or loaded during execution by the dynamic link loader. if you do an strace command on any system binary . you notice that it will try to link some system shared libraries like libc.so
openat(AT_FDCWD, “/lib/x86_64-linux-gnu/libc.so.6”, O_RDONLY|O_CLOEXEC) . Shared object libraries are not part of the application; however, they must be available during the compilation process

## A common misconception

there is a common misconception when studying C language that many people think that **stdio.h** or **stdlib.h** are libraries and when typing #include \<stdio.h\> they are including the library stido.h which is not true . they are called header files . The C standard library is a collection of functions, which are declared in header files, and stdio.h is one of them. The name stands for "Standard Input Output", so in that file you can find all the declarations of functions that deal with input, output and files. the name of C standard library is **libc.so** or **libc.a** you can find in the directory /lib/x86_64-linux-gnu/.

## How to create your own static library

so let’s say you have some functions that you always use but those functions are created and only used by you. you don’t want to always rewrite them in your code . so you decide to make your own static library .
we will create 2 file add.c and sub.c which will either add or sub two ints.
![](/assets/img/static-libraries/1.png)

then we will extract the object files(machine code files)
![](/assets/img/static-libraries/2.png)

after that we create a library(archive) from those object files
![](/assets/img/static-libraries/3.png)

now that the library is ready let’s remove the .c files and add a header file that will call the function add and sub from their library

![](/assets/img/static-libraries/4.png)

we can list the object files in the libbasic.a library

![](/assets/img/static-libraries/5.png)

the final step is to statically link the library to a file and see if everything work

![](/assets/img/static-libraries/6.png)

we created a function main that will call add and sub . in the first compilation it didn’t work because the compiler didn’t know where to get add and sub from as they are not included in the standard C library .we already said that By default the gcc compiler or more specifically its linker needs to be directed to which libraries to search other than the standard C library — which is included by default .
so then we linked the basic library to the binary and it works fine.

![](/assets/img/static-libraries/final.png)

## Advantages and drawbacks

The advantage of static linking is that it removes external dependency on libraries . the behaviour of the library you’re using is never going to change because someone changed the library on the disk. imagine that you compiled a program statically with you current version of LIBC . then you move that file to another system with another LIBC version . your binary(program) won’t be affected because it doesn’t relay on the external system LIBC . he got his own inside him . That’s also one of the disadvantages of static linking; if the OS changes and a new version of the library is needed to work with it properly, you have to provide an upgraded version of your binary. Similarly if a bugfix is added to the library, you don’t automatically get that bug fix if you’ve statically linked you need to recompile your program with the updated library in order to change it . Static linking will speed up the program by eliminating an extra jump of indirection in code execution but this really doesn’t matter as you don’t even notice it . and the final major drawback is that statically linked programs have a huge size compared to dynamically linked program which do the same thing. just imagine that you have to load all the LIBC inside your file if you want to use it.

## Extra information

you may had stambled across DLL files when using windows . well those are shared libraries which are dynamically loaded in an EXE file.




