---
layout: post
title: "What is /etc/passwd and /etc/shadow files"
categories: explained
tags: [under the hood,linux]
---

Hi everyone. In this blog post I will be explaining what is the difference between /etc/passwd and /etc/shadow as it always confuse new comers to linux

## prerequisite

In Linux, a user is identified using a unique identifier called as user id (UID). Each user belongs to at least one group called the user’s primary group. Like user, a group is also identified with the help of a unique identifier called as group id (GID). UID and GID controls the accessibility of the files and folders in Linux.

## /etc/passwd

/etc/passwd file stores essential information, which required during login. In other words, it stores user account information. The /etc/passwd is a plain text file. It contains a list of the system’s accounts, giving for each account some useful information like user ID, group ID, home directory, shell, and more. The /etc/passwd file should have general read permission as many command utilities use it to map user IDs to user names. However, write access to the /etc/passwd must only limit for the superuser/root account.

Let's take this line from an /etc/passwd output ```kam:x:502:502:Kam Aggarwal:/home/kam:/bin/bash```

Each entry in Linux passwd file corresponds to a user and contains seven fields separeated by **:** as shown below.

### field1: Username

This contains the user’s login id. This is the username which is used for login.
Username uniquely identifies a user on a Linux system. We can’t have multiple users with same username.

### field2: password

this field contain the encrypted password for the user
if the value is **x** it means that the password is stored in **/etc/shadow** file like that:
```kam:$1$8zowrENV$XL8nvq94dwqU4kTmDlBQk.:15595:0:99999:7:::```

### field3: User-id

Each user must be assigned a user ID (UID). UID 0 (zero) is reserved for root and UIDs 1-99 are reserved for other predefined accounts. Further UID 100-999 are reserved by system for administrative and system accounts/groups.

### field4: Group-id

Group-Id represents a unique number identifying the primary group id for the user.
Linux maintains the group name and group id mapping in a separate world readable file /etc/group. Following is the entry for GID ’502′ in the group file.
```kam:x:502:```

### field5: User information

The comment field. It allow you to add extra information about the users such as user’s full name, phone number etc. This field use by
**finger** command.

### field6: home Directory

User’s home directory contains all user specific configuration files.
A user is provided full access to it’s home directory. Which means that the user is free to add, modify and delete any file or folder in its home directory.

### field7: User's shell

The absolute path of a command or shell (/bin/bash). Typically, this is a shell. Please note that it does not have to be a shell. For example, sysadmin can use the nologin shell, which acts as a replacement shell for the user accounts. If shell set to /sbin/nologin and the user tries to log in to the Linux system directly, the /sbin/nologin shell closes the connection.
there is not only /bin/bash it can be any type of shell

## /etc/shadow

is the file where important information (like an encrypted form of the password of a user, the day the password expires, whether or not the passwd has to be changed, the minimum and maximum time between password changes, ...) is stored when a new user is created.
let's take this example: ```bob:$1$9691cSVC$zN/LWa6NNAYADAZXUMGIV0:15582:10:30:7:20:15631:```

Each line in the /etc/shadow file corresponds to a user account. Each entry is divided in to 9 fields with the help of a delimiter **:**.

### field1: Username

Login name (no surprise)

### field2: Password

It is your encrypted password. The password should be minimum 8-12 characters long including special characters, digits, lower case alphabetic and more. Usually password format is set to $id$salt$hashed, The $id is the algorithm used On GNU/Linux as follows:

>1. $1$ is MD5
>2. $2a$ is Blowfish
>2. $2y$ is Blowfish
>4. $5$ is SHA-256
>5. $6$ is SHA-512

if a password field start with **!** it means it's locked.

### field3: last password change

Last password change (lastchanged) : Days since Jan 1, 1970 that password was last changed

### field4: Minimum

The minimum number of days required between password changes i.e. the number of days left before the user is allowed to change his/her password

### field5: maximum

The maximum number of days the password is valid (after that user is forced to change his/her password)

### field6: warn

The number of days before password is to expire that user is warned that his/her password must be changed

### field7: Inactive

The number of days after password expires that account is disabled

### field8: expire

days since Jan 1, 1970 that account is disabled i.e. an absolute date specifying when the login may no longer be used.

you can use command **chage -l username** to know more about a user password expiry information

## how to create a /shadow password

you can create a new password and encrypt to paste it directrly in /etc/shadow password field

write the command ```openssl passwd -6 mypass``` to generate a SHA-512 from the word mypass

paste the output ```$6$pe5OcjXg8Ovu6QkA$W8ft0h1ME4sgqoqb5aJXjVNPOiKh.G8Mk6NIQu5vhNXju/HbozoHbhT.sfQ9/HoBPI/GqdftXCmyImc4BGJui0``` in password field of you desired user

## warning

if you leave your passwd file writable by anyone, thinking that /etc/shadow only must be secure, well if someone change the value **x** in
/etc/passwd to a custom encrypted password generated by openssl like we demonstrated. well the password will change and Unix won't look at
/etc/shadow password entry.  