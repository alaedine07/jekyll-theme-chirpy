---
layout: post
title: "Solve your first Crackme"
categories: explained
tags: [under the hood, low level, C, programming, linux, security]
---

Hi everyone. In this blog post I will be explaining how to solve a simple beginner crackme.

## Reverse engineering

What we will be doing now is called [__reverse engineering__](https://en.wikipedia.org/wiki/Reverse_engineering). As a programmer you develop you program by first making a design of it, you try to understand first how your program will behave in a lot of conditions, then you will implement this design into code with the help of a programming language which will produce a final executable/website/hardware... that you deliver to the users. So the users knows nothing about how your product was designed, how the code is written and implemented, they only see what you want them to see, they only give inputs and get output.

But there is one users who is Not OK with that. he want to understand how your program was made and try to mimic the source code. maybe he is just doing that for his own curiosity, maybe he want to Patch the executable and add new features to it, maybe he is trying to bypass a security check that you are using (licence key, password verification, custom encryption ...), Or even another company who got jealous after seeing your product on the market and they are trying to know how it works so they can make their own and maybe even a better one.

what all this people will try to do is __reverse engineering__ your product. they will try to understand what he is doing based on the input/output and using other tools made for this purpose.

Reversing is an *art*

## Prerequisite

Tho I will try to make it really simple and explain a lot of terms, but a Basic understanding of assembly will make reading this article more enjoyable and a better learning experience to you. I added links at the end of the Post to learn assembly both youtube videos and blog posts.
also a really basic GDB manipulation is required, and of course programming

## Let's Go

I will take this [cracke me file](https://github.com/holbertonschool/0x04.c/blob/master/101-crackme) as an example here.

### Checking the behaviour

so we have an executable file that we want to know how it's working, the first thing to do is to interact with it.
let's try to execute it first

![](/assets/img/crackme/enum1.png)

so we can already see that it want us to provide an argument called password let's do that.

![](/assets/img/crackme/enum2.png)

so we sent the password but it's a wrong one, we could make our computer spend all the night trying millions of passwords but that's dumb and it's not the purpose of reversing.
another good thing to know is information about the executable we are dealing with. using the command ```file``` is enough to get a quick idea.

![](/assets/img/crackme/file_command.png)

A 64-bit executable, dynamically linked and not stripped.

### Breakpoint
Dynamically linked means that the binary is relying on shared-libraries to do some parts of the job. Now the opposite of dynamically linked is statically linked.

Statically linked means that the binaries is not in need of external libraries to work. Meaning it has all the dependencies within it.

Not stripped means that the binary has debugging symbols, and that makes debugging it easier. The opposite of that is stripped.

Stripped means that the binary is not having debugging symbols which makes it harder to reverse and debug. For example there would be no function names in a stripped binary and you would need to predict the functions based on their functionalities.

### Debugging time

So after having a basic idea of what that executable is doing, we will open the file in GDB with ```gdb 101-crackme```. GDB offers extensive facilities for tracing and altering the execution of computer programs. The user can monitor and modify the values of programs' internal variables, and even call functions independently of the program's normal behavior.

first let's check the functions used on this binary with ```info functions```

![](/assets/img/crackme/gdb1.png)

there is a lot of functions we don't know about. they are all added by the shared library, but there is a **main** and **checksum** functions that are surely created by the programmer.

let's also check the global variables used this file with ```info variables```

![](/assets/img/crackme/gdb_variables.png)

Uh!!! nothing interesting let's move on and disassemble the main functions which is the entry function.

### Breakpoint

in a certain era, C(which is considered a low level programming language) was considered high level because people were coding in Assembly, compiling the code will turn the C code into assembly instructions which is the closest thing to machine language. Saying the word disassemblying means that i will check the result of the code at an assembly level. here I will use the INTEL syntax not the AT&T because I find it more clear

### Understanding main 

let's use the command ```set disassembly-flavor intel``` to set the assembly into INTEL syntax and then use the command ```disassemble main```
![](/assets/img/crackme/disas-main.png)

Oh WTF is that!!!. calm down it's the assembly of the main functions. we don't need to understand all of it now we will just take what we need. do you see that line ```cmp    DWORD PTR [rbp-0x14],0x2``` it's comparing something with 2... think about what could be compared with 2. the *ARGUMENT* yes !!! we can translate this with ```if argc == 2 ``` which mean we have entered an argument, the next line is 
```je     0x4005d2 <main+53>```. __je__ stands for JUMP if Equal. and this line will jump to the instruction after 53 line from the starting of main. but what could happen if it's not equal to 2?? well it will not execute the JUMP and start executing what is next from the line +21.
we can see that after few instructions it call **printf** think about it what could that printf be used for ?? the *ERROR Message* yes !!! which is ```Usage: ./101-crackme password``` after that it will jump to the line main+118 which is a LEAVE instructions it means the program will quit the **function** in this case if the program quit main it means it will quit the execution.

now let's go back to the line +53. the instruction ```mov    rax,QWORD PTR [rbp-0x20]``` means that it will move something from the stack to the **rax** register(register are the memory space used directly by the processor). after few other instructions it will call the function
**checksum**. let's see what's happening here by adding a breakpoint in the instruction that call checksum. gdb will stop the program just before executing the instruction and we can examine the memory there.
![](/assets/img/crackme/first-break.png)

Don't panic I will tell what we did. first we added a breakpoint on the instruction that call **checksum** with 
```break *0x00000000004005e0``` then i started the program with ABCD as argument with ```start ABCD```. gdb by default break at main if it exists so i had to use ```continue``` to move on the running. then it hits another breakpoint which is the one i set . after that i examined the register *rax* with ```x/s $rax``` (*x* stands for examine and *s* is the type string) and we can notice that rax contains "ABCD" which is the argument i passed. also *rdi*(another processor register) have "ABCD" because there is an instruction executed ```mov rdi, rax``` which put the value of *rax* into *rdi*

### Understanding checksum

![](/assets/img/crackme/disas-checksum.png)

from the pattern of this function we can deduce that it's doing a loop. WAIT how did you know that!!!. ok so first with the line 
```mov    QWORD PTR [rbp-0x8],0x0``` it seems like the function is initializing something to 0 (maybe a sum??), then the function will jump to the line +38 and do some instructions there, we can see also the line ```add    QWORD PTR [rbp-0x18],0x1``` which basically add 1 to where our string is used so it's like ```chr++``` with *chr* as pointer to string. then it will make a test ```test al, al``` this instructions is used to check if a value is 0. if it's not 0 (*jne* stands for jump not equal), it will go back to +18. I guess you get the pattern here ? YES you are right!!! it's looping a string(the argument) until it's end(NULL byte). but what's happening in that loop??. that's when GDB become very helpful...

I switched to this layout by using the command ```layout next``` 3 or 2 times it will help me to see the registers and memory state at the same time the program is running. you can step the instructions line by line with the command ``ni``

![](/assets/img/crackme/layout.png)

after a lot of times debugging, adding breakpoints, checking register i was able to find something out of this 

![](/assets/img/crackme/here.png)

we can see that the buffer on the stack(variable) used to sum which is *rbp-0x8* is getting the value **0x41** after the 1st iteration which is 61 in decimal which corresponds to *A* in ASCII and it becomes **0x83**(131 in decimal) after the second iteration which is 65 + 66 which corresponds to ascii(A) + ascii(B). i'm using the command 
```x/wx $rbp-0x8``` to get the value of rbp in HEX.
to replicate this and after adding a breakpoint on checksum.

1. you need to add a breakpoint at ```0x0000000000400587 <+33>:    add    QWORD PTR [rbp-0x18],0x1```
2. keep stepping with ``ni``
3. when you hit the breakpoint check the value of sum with ```x/wx $rbp-0x8``` 

![](/assets/img/crackme/final-1.png)

now we are in the instructions before the return. the rax register is holding the return value of the function checksum which is 0x10a(266 in decimal) which is equal to ascii(A)+ascii(B)+ascii(C)+ascii(D) this means that the function checksum return the sum of ascii values of the argument string.(finally)

![](/assets/img/crackme/cmp-break.png)

after executing checksum, the result will be saved on *$rbp-0x8* and then it will be compared with *0xad4*(2772 in decimal)

### Breakpoint

after all what we did we can fineally understand what this program is doing.

1. take a string as an argument

2. make a sum from the ascii values of the argument characters

3. compare it with 2772

4. if it's not 2772 than the password is false 

so if we want to beat this check . our argument must contains characters that if summed together their ascii value must be 2772.

take your time to solve this before reading the solution, redo the same steps with gdb again and again.... 

### keygen

so now after understanding what this binary is doing and checking. we will create keygen that will generate a new valid password every time it's executed . I coded this [__keygen__](https://github.com/alaedine07/holbertonschool-low_level_programming/blob/main/0x05-pointers_arrays_strings/101-keygen.c). let's try to understand it.

first the code will generate a random number from a seed(that's how it's done in C I didn't came up with that), then with a mathematical operation we can reduce the value of that number to make it fit on a range of ascii chars after 57 ```number = rand() % 58 + 65;```.
after that this ascii value will be added to variable ```somme```, this process will be repeated until somme is equal to 2772. But this code isn't really precise because **somme** can be added with a value that will make it greater than 2772. to get around that I wrapped all the process
in a ```while (somme != 2772)``` it means that if ```somme``` become greater than 2772 all the process will be repeated from 0.

let's see the example here:   

![](/assets/img/crackme/congrats.png)

we can see that the process was repeated 155 times before finding a string that the somme of it's char is equal to 2772. the first time it found a match after 4 tries, I guess i was just lucky there. 

but when adding that string to the argument of the crackme file it didn't work !!!. well we can notice that there is a *\`* in the string, just escaping it will do the trick and we get Tada! congrats

well we don't want to copy the string from keygen every time like that, we want to pass the keygen as an argument and it will do all the work directly.

![](/assets/img/crackme/final.png)

first i made the keygen only print the valid string and passed it as an argument ```./a.out``` to crackme file but wait a minute that's not how it works...
we don't want to pass litearlly "./a.out" but we need the result of ```./a.out``` so let's wrap the argument inside ```$()``` so it get executed before the keygen file . this means that the argument of ```./101-crackme``` will be the result of ```./a.out``` which is the valid string printed.

Holééé the crackme if fineally solved it, was great challenge to get an introduction to reversing, assembly, gdb and thinking out of the box
I hope you enjoyed this Post and learned new things.

## Resources

[quick introduction about how cpu works by liveoverflow](https://www.youtube.com/watch?v=6jSKldt7Eqs&ab_channel=LiveOverflow)

[asm tutor course ](https://asmtutor.com/)

[a great blog serie](https://0xax.github.io/asm_1/)

[a great and detailed intel assembly youtube playlist](https://www.youtube.com/watch?v=wLXIWKUWpSs&list=PLmxT2pVYo5LB5EzTPZGfFN0c2GDiSXgQe&ab_channel=DavyWybiral)

[reversing a simple crackme with pen and paper by liveoverflow](https://www.youtube.com/watch?v=VroEiMOJPm8&ab_channel=LiveOverflow)

And of course the mighty [google](www.google.com)














