---
author: chris.hill
comments: true
date: 2012-10-09
excerpt: 遇到一个16TB的分区，好头疼呀。查了好多资料，才发现原来是需要使用GPT格式进行分区才能正常使用这么大的分区呢！总结一下，简单介绍一下GPT和MSDOS，供参考。
layout: post
slug: gpt-and-msdos
title: GPT和MSDOS
categories:
- IT运维
tags:
- Linux
---

* * *





#简介





##BIOS+MBR组合



BIOS是英文"Basic Input Output System"的缩略语，直译过来后中文名称就是"基本输入输出系统"。  

其实，它是一组固化到计算机内主板上一个ROM芯片上的程序，它保存着计算机最重要的基本输入输出的程序、系统设置信息、开机后自检程序和系统自启动程序。  

其主要功能是为计算机提供最底层的、最直接的硬件设置和控制。



<!-- more -->


主引导记录（Master Boot Record，缩写：MBR），又叫做主引导扇区，是计算机开机后访问硬盘时所必须要读取的首个扇区，它在硬盘上的三维地址为（柱面，磁头，扇区）＝（0，0，1）。  

主引导扇区记录着硬盘本身的相关信息以及硬盘各个分区的大小及位置信息，是数据信息的重要入口。  

现有PC架构通常使用BIOS+MBR这种组合模式，MSDOS是该模式中的磁盘分区表类型。





##EFI+GPT组合





EFI名为可扩展固件接口，是微软和英特尔共同推出的主板升级换代方案。  

EFI，即可扩展固件接口(Extensible Firmware Interface)，EFI的位置很特殊，不像是BIOS那样是固件又是接口，EFI只是一个接口，位于操作系统与平台固件之间。





GUID分区表（GUID Partition Table，缩写：GPT）是一个实体硬盘的分区结构。  

它是可扩展固件接口标准EFI的一部分，用来替代BIOS中的主开机记录分区表。





#特点





##MSDOS特点



  * 最大支持2TB卷大小。    

  * 每个磁盘最多只能有4个主分区(或3个主分区，1个扩展分区和无限制的逻辑分区）。    

  * Linux系统下可以使用fdisk\cfdisk对该分区类型进行操作。   





##GPT特点







  * GPT支持磁盘大小达到了18EB(1EB=1024PB=1,048,576TB)，但需要注意的是，单一分区的磁盘大小还受文件系统类型的限制，比如NTFS文件系统最大只支持256TB的大小。    

  * GPT对分区数量没有限制。  

  * Linux系统下可以使用parted工具对该分区类型进行操作。   





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



