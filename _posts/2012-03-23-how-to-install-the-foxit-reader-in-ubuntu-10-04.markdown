---
author: chris.hill
comments: true
date: 2012-03-23
excerpt: 介绍了如何在GNOME2桌面环境下安装Foxit PDF 阅读器
layout: post
slug: how-to-install-the-foxit-reader-in-ubuntu-10-04
title: 在ubuntu10.04_64位系统上安装Foxit Reader阅读器
categories:
- IT运维
tags:
- Linux
---

* * *





# 背景

由于Foxit PDF阅读器官方只提供了32位的deb软件包，若想在64位ubuntu 10.04系统上使用该软件，可以参考本文档。


## 软件介绍


福昕PDF阅读器是一款小巧、快速且功能丰富的PDF阅读器，让您能够随时打开、浏览及打印任何PDF文件。  

如同Foxit Reader官方自我介绍的：_Better than Adobe PDF Reader and Acrobat_一样，小巧、快速、功能丰富，完全可以替代Adobe Reader。  

强烈推荐大家在Linux使用该款软件作为默认的pdf阅读器。

安装环境  

* 操作系统：Ubuntu10.04 x86_64（Lucid）

<!-- more -->




# 安装





下载地址：
<http://cdn04.foxitsoftware.com/pub/foxit/reader/desktop/linux/1.x/1.1/enu/FoxitReader-1.1.0.tar.bz2>


*chris.hill* 将下载的tar.bz2的软件包放置在/usr/src目录下面。


执行命令：

```sh
sudo wget http://cdn04.foxitsoftware.com/pub/foxit/reader/desktop/linux/1.x/1.1/enu/FoxitReader-1.1.0.tar.bz2 -O /usr/src/FoxitReader.tar.bz2
```



之后解压相关该压缩包



```sh
sudo tar xvjf /usr/src/FoxitReader.tar.bz2 -C /opt/FoxitReader
```



查看解压后的文件



```sh
ls /opt/FoxitReader1.1-releasels
```

内容如下：
    
```sh
/opt/FoxitReader/1.1-release     
FoxitReader  fpdfcjk.bin  fum.fhd  po  Readme.txt
```
输出:

```
FoxitReader  fpdfcjk.bin  fum.fhd  po  Readme.txt
```




该目录下面的FoxitReader文件为编译好的应用程序直接执行命令：



```sh
./FoxitReader
```




即可使用该软件。





# 优化DIY





## 创建FoxitReader的开始菜单





首先编辑启动脚本文件



```sh
vim /usr/bin/FoxitReader.sh
```

脚本内容如下：
    
```sh
#!/bin/bash
cd /opt/FoxitReader/1.1-release
./FoxitReader
```




创建菜单编辑Foxit-Reader菜单文件


```sh
sudo vi /usr/share/applications/Foxit-Reader.desktop
```


文件内容如下：    

```ini
[Desktop Entry]
Name=FoxitReader
Comment=Read PDF Document
Exec=FoxitReader.sh
Icon=/usr/share/pixmaps/fx-icon.png
Terminal=false
Type=Application
Categories=Application;Office;
```




``` 
注意：需要准备一个菜单栏显示用的图标，_chris.hill_本人是从FoxitReader的i386版本deb包里面扒出来的图标放置在路径/usr/share/pixmaps/fx-icon.png下。
``` 
 






至此，所有的工作已经完成。





* * *





作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



