---
author: chris.hill
comments: true
date: 2013-10-27 
excerpt: '为了增加Linux系统安全性，建议将/tmp目录单独的挂载于一个独立的系统分区之上。

  但是仅仅挂载还不够，需要在挂载时为该分区指定nodev/nosuid/noexec选项，才能提高tmp文件目录的安全性。

  本文将介绍为了提高Linux系统安全性，如何为/tmp添加nodev/nosuid/noexec挂载选项。'
layout: post
slug: how-to-add-nodev-nosuid-noexec-options-to-tmp-when-mount
title: '[Linux安全]为/tmp添加nodev/nosuid/noexec挂载选项'
categories:
- IT运维
tags:
- Linux
---

* * *





为了增加Linux系统安全性，建议将/tmp目录单独的挂载于一个独立的系统分区之上。但是仅仅挂载还不够，需要在挂载时为该分区指定nodev/nosuid/noexec选项，才能提高tmp文件目录的安全性。





# 背景





临时存储目录，如 /tmp，/var/tmp和/dev/shm等，往往是恶意程序袭击的目标。黑客们在执行攻击的时候，往往将可执行程序放置于该目录之下。恶意用户一般也利用这些临时存储目录执行自己的恶意程序，从而发起对线上系统的破解和攻击。





由于/tmp目录时系统运行必备的目录，那么怎么增强该目录的安全性，尽可能减少系统潜在危险呢？





我们可以通过以下的方法进行设置



<!-- more -->



# 配置过程





1.挂载时为/tmp分区添加nodev/nosuid/noexec选项





编辑/etc/fstab文件
`




    
    
```sh
vi /etc/fstab
```

找到/tmp挂载目录的相关配置





例如_chris hill_当前系统的配置如下




```
UUID=9abc328b9-3d22-4224-acd6-c48d7b4d3aa4 /tmp   ext4    defaults      1 2
```







将_nodev,nosuid,noexec_选项增加到第四列_defaults_参数的后面




```
UUID=9abc328b9-3d22-4224-acd6-c48d7b4d3aa4 /tmp   ext4   defaults,nodev,nosuid,noexec   1 2
```







2.挂载时为/dev/shm文件系统添加_nodev/nosuid/noexec_选项





与第1步的/tmp类似，编辑/etc/fstab




```
tmpfs  /dev/shm  tmpfs  defaults,nodev,nosuid,noexec 0 0
```







3.有关/var/tmp设置





有些系统需要使用到/var/tmp目录。





建议设置/var/tmp和/tmp目录同步。





编辑/etc/fatab文件




```
/tmp /var/tmp none rw,noexec,nosuid,nodev,bind 0 0
```






4.无需重启系统，使配置生效





设置 /var/tmp 映射到 /tmp 目





    
```sh
mount -o rw,noexec,nosuid,nodev,bind /tmp/ /var/tmp/
```






重新挂载/tmp目录





    
    
```sh
mount -o remount,noexec,nosuid,nodev /tmp
```





重新挂载/dev/shm设备


    
```sh
mount -o remount,noexec,nosuid,nodev /dev/shm
```    










5.查看结果

    
    
```sh
mount
mount | egrep --color -w '^(tmpfs|/tmp)|/tmp'
```





    **Tips**
        你也可以创建一个类似于/tmpfile/tmpfile.bin的文件，通过挂载该文件作为应用程序运行的临时存储空间。
            例如
                /tmpfile/tmpfile.bin  /tmp ext4  rw,noexec,nosuid,nodev,bind  0 0




# 参考资料





<http://www.cyberciti.biz/tips/the-importance-of-linux-partitions.html>





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



