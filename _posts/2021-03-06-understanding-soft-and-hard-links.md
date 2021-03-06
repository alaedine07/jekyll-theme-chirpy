---
layout: post
title: "Understanding soft and hard links in linux and how they are used?"
categories: explained
tags: [under the hood,linux]
---

One of the concepts a person will surely encounter on his journey of learning linux is dealing with soft or hard links . It easily confuse people so I hope this Post will make it more clear :)

## Hard links

Let’s say we have a file which is stored in a spot on the hard drive, if you make a hard link of that file you will create another one who point to the same spot as the first file so if your first file is 10Gb the hard copy will also be another 10Gb also this means that if you make changes on any of those 2 files it will be applied on both of them since they are stored on the same data spot.
![](/assets/img/soft_hard_links/hard_links.png)

You can notice here that when i wrote “hello world” in the hardlink.txt file it also went to file.txt that’s because “hello world” is written in the data spot shared between the 2 files.

## Soft links:

Also known as **symlinks** . This type of links is same as when you create a shortcut for windows software because you don’t want to navigate to programfiles each time . It’s just a references to a file or directory name. this link will break if the original file/directory is missing.
![](/assets/img/soft_hard_links/soft_links.png)

Notice how the soft link turned red when the original was removed . It’s called a broken link now.

## How do we use them

Hard links are used to create “backups” on filesystems without using any additional storage space. If the file or folder is no longer accessible from its original location, then the hard link can be used as a backup to regain access to those files.
Soft links are used more heavily to enable access for files and folders on different devices or filesystems. These types of symbolic links are also used in situations where multiple names are being used to link to the same location.