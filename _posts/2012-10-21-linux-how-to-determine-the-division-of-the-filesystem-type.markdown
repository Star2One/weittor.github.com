---
author: chris.hill
comments: true
date: 2012-10-21
excerpt: 'Linux系统下，通过常用的"fdisk -l"命令可以查看分区的类型，但是无法获得文件系统的信息。

  总结一下能够获得分区文件系统类型的方法，供大家参考。'
layout: post
slug: linux-how-to-determine-the-division-of-the-filesystem-type
title: Linux系统如何查看分区的文件系统类型
categories:
- IT运维
tags:
- Linux
---

* * *





# 背景





Linux系统下，通过常用的"fdisk -l"命令可以查看分区的类型，但是无法获得文件系统的信息。





总结一下能够获得分区文件系统类型的方法，供大家参考。


<!-- more -->



# 方法


  * 命令：`mount `  

  * 命令：`df -Th`  

  * 工具：`parted`   

  * 文件：`/etc/fstab`





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



