---
author: chris.hill
comments: true
date: 2013-08-23 
excerpt: '在linux系统中，我们可以通过挂载Tmpfs或Ramfs文件系统实现对内存的访问使用。

  对于一些关键业务来说，可以考虑将主要的读写进程通过Tmpfs或Ramfs文件系统放入内存中执行。'
layout: post
slug: overview-of-ramfs-and-tmpfs-on-linux-cn
title: Linux系统-RAMFS和TMPFS简介
categories:
- IT运维
tags:
- Linux
---

* * *





在linux系统中，我们可以通过挂载Tmpfs或Ramfs文件系统实现对内存的访问使用。  

对于一些关键业务来说，可以考虑将主要的读写进程通过Tmpfs或Ramfs文件系统放入内存中执行。





# 挂载tmpfs




    
    
```sh
mkdir -p /mnt/tmp
mount -t tmpfs -o size=20m tmpfs /mnt/tmp
```
    





查看挂载情况




    
```sh
df -k
```



输出如下：
    
```    
    Filesystem      1K-blocks  Used     Available Use%  Mounted on
    /dev/sda2       32705400   5002488  26041576  17%   /
    /dev/sda1       194442     18567    165836    11%   /boot
    tmpfs           517320     0        517320    0%    /dev/shm
    tmpfs           20480      0        20480     0%    /mnt/tmp
```    





以上输出的最后一行显示/mnt/tmp文件系统已经挂载成功。




<!-- more -->

# 挂载Ramfs




    
    
```sh
mkdir -p /mnt/ram
mount -t ramfs -o size=20m ramfs /mnt/ram
```





查看挂载结果




    
    
```sh
mount
```



输出如下：
    
```    
    /dev/sda2 on / type ext3 (rw)
    proc on /proc type proc (rw)
    sysfs on /sys type sysfs (rw)
    devpts on /dev/pts type devpts (rw,gid=5,mode=620)
    /dev/sda1 on /boot type ext3 (rw)
    tmpfs on /dev/shm type tmpfs (rw)
    none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
    sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw)
    fusectl on /sys/fs/fuse/connections type fusectl (rw)
    tmpfs on /mnt/tmp type tmpfs (rw,size=20m)
    ramfs on /mnt/ram type ramfs (rw,size=20m)
```    





通过以上输出最后一行，可以查看当前挂载/mnt/ram的相关情况。





通过配置/etc/fstab文件，可以实现开机挂载tmpfs或ramfs文件系统分区。





# Ramfs和Tmpfs的区别





Ramfs可以动态进行容量扩展，只要不大于系统内的可用物理内存大小即可。  

Tmpfs无法进行动态扩展，只能使用挂载时指定的分区大小。  

Tmpfs使用swap或物理内存作为存储空间。  

Ramfs不能使用swap空间，只能使用物理内存空间。





# 参考资料





[http://www.thegeekstuff.com/2008/11/overview-of-ramfs-and-tmpfs-on-linux/](http://www.thegeekstuff.com/2008/11/overview-of-ramfs-and-tmpfs-on-linux/)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



