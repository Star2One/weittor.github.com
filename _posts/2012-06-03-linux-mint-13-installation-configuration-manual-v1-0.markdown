---
author: weittor
comments: true
date: 2012-06-03
excerpt: 主要介绍Linux Mint 13 Maya系统安装后的配置工作，推荐了一些Linux系统内的经典软件，帮助大家建立一个和谐的Linux办公环境。
layout: post
slug: linux-mint-13-installation-configuration-manual-v1-0
title: Linux Mint 13安装配置v1.0
categories:
- IT运维
tags:
- Linux
---

* * *





# 更新源地址





编辑/etc/apt/source.list文件， 将ubuntu12.04的默认源地址更改为163网易的镜像源。  

执行命令，使用Mint13默认的文本编辑器pluma编辑。


```sh
sudo pluma /etc/apt/source.list
```



内容如下:

```
deb http://packages.linuxmint.com/ maya main upstream import
deb http://mirrors.163.com/ubuntu/ precise main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ precise-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ precise-security main restricted universe multiverse
deb http://archive.canonical.com/ubuntu/ precise partner
deb http://packages.medibuntu.org/ precise free non-free
```




更新源



```sh
sudo apt-get update
```


<!-- more -->

# 更新系统的语言支持





依次点击“Menu”--“控制中心”--“Language Support”。  

按照提示，安装目前缺省的软件包即可。  

之后需要重启系统。





## 安装ibus输入法





执行命令：




```sh    
sudo apt-get install ibus
```




# 更新附加的硬件驱动支持





安装相应的显卡驱动。  

依次点击“Menu”--“控制中心”--“Additional Drivers”。  

按照提示，激活下载的nvidia显卡驱动或者ATI显卡驱动即可。





# 安装常用的互联网软件





## 开源google浏览器 ：chromium-browser





提示：Mint13默认已经安装了Firefox浏览器的Flash插件，无需再安装。  

执行命令：




    
```sh
sudo apt-get install chromium-browser
```




## 微博客户端 ：gwibber





提示：还需要安装sina微博和sohu微博插件。  

执行以下命令：



```sh
sudo apt-get install gwibber gwibber-service-sina gwibber-service-sohu
```




## FTP客户端 ：filezilla





执行命令：



```sh
sudo apt-get install filezilla
```




## 下载客户端 ：multiget





Linux系统下类似于迅雷的下载软件。  

执行命令：



```sh
sudo apt-get install multiget
```


<!-- more -->



## 多协议聊天客户端 ：pidgin





执行命令：



```sh
sudo apt-get install pidgin
```




# 安装办公类软件





## 编辑器:vim，gedit





执行命令：



```sh
sudo apt-get install vim gedit
```



## 词典：Goldendict





执行命令：




```sh    
sudo apt-get install goldendict
```




## Office办公套件：永中OFFICE2012青年版





啰嗦：在WPS for Linux还没有面世之际，个人觉得永中OFFICE2012做的相当的不错。  

推荐一下。





    安装方法
        参考[奶牛博客一篇文章](http://goo.gl/F3FJL)。  

        提示：安装后，需要自己在永中网站下载字体包、模版包进行安装。  

        不然的话，界面会相当的难看哦。


# 安装图形处理类软件


  * 类似于visio的图表编辑软件：dia  

  * 挑战photoshop的开源图像处理软件：GIMP   

  * 矢量图绘制软件：inkscape   

  * 截屏软件：shutter   

  * 桌面录像软件：recordmydesktop  

  * chm阅读软件：xchm   





执行命令



```sh
sudo apt-get install dia gimp inkscape shutter recordmydesktop xchm
```




# 安装附件类软件





## 安装virtualBox虚拟机



```sh
sudo apt-get install virtualbox
```




# 安装开发编译环境





## 安装deb软件包编译环境





执行命令：


```sh
sudo apt-get install dpkg-dev file gcc g++ libc6-dev make patch perl automake autoconf dh-make debhelper devscripts fakeroot gnupg xutils lintian
```




# 安装游戏





## 安装wine



```sh
sudo apt-get install wine
```




## Linux版CS游戏AssaultCube



```sh
sudo apt-get install assaultcube
```




## Linux版卡丁车游戏SuperTuxKart



```sh
sudo apt-get install supertuxkart
```




# 系统工具类软件





## 清理系统垃圾bleachbit


```sh
sudo apt-get install bleachbit
```




## Ubuntu tweak





安装方法：


```sh
sudo add-apt-repository ppa:tualatrix/ppa
sudo apt-get update
sudo apt-get install ubuntu-tweak
```




## 网盘Ubuntu One



```sh
sudo apt-get install python-ubuntuone-control-panel ubuntuone-client ubuntuone-client-gnome ubuntuone-control-panel ubuntuone-couch ubuntuone-control-panel-gtk
```




* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



