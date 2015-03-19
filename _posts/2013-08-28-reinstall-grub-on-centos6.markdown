---
author: chris.hill
comments: true
date: 2013-08-28
excerpt: 在Centos6系统上重新安装GRUB。
layout: post
slug: reinstall-grub-on-centos6
title: '[How-To]Centos6重新安装GRUB'
categories:
- IT运维
tags:
- Linux
---

* * *





#准备工作

1)通过Centos6第一张光盘引导，选择修复模式"Rescue installed system"   

2)期间会有相应的提示，选择将所有文件系统以只读方式挂载   

3)Centos6光盘会默认将所有硬盘分区挂载于/mnt/sysimage目录下面   

4)通过chroot，进入当前系统环境   

    
```sh
chroot /mnt/sysimage
```







#重新安装GRUB





1)安装GRUB到SCSI\SATA硬盘




    
    
```sh
grub-install /dev/sda
```


<!-- more -->


2)安装GRUB到SCSI\SATA硬盘的第2个分区




    
```sh
grub-install /dev/sda2
```





3)安装GRUB到IDE硬盘




    
```sh
grub-install /dev/hda
```





4)安装GRUB到配置装有HP阵列卡的硬盘




    
```sh
grub-install /dev/cciss/c0d0
```







#参考资料





<http://wiki.centos.org/TipsAndTricks/ReinstallGRUB>  

<http://wiki.centos.org/HowTos/GrubInstallation>





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



