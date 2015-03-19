---
author: chris.hill
comments: true
date: 2012-12-14
excerpt: 亲，别再用DVD装系统了，用U盘也可以的哦。本文为你介绍通过U盘安装ubuntu系统的详细过程。
layout: post
slug: how-create-a-usb-stick-on-ubuntu
title: 通过U盘安装ubuntu系统
categories:
- IT运维
tags:
- Linux
---

* * *





# 简介





亲，别再用DVD装系统了，用U盘也可以的哦。





下载系统镜像地址：  [地址](http://www.ubuntu.com/download)





> chris.hill下载的是ubuntu12.04系统
> 
> 


<!-- more -->




# 安装Startup Disk Creator工具





在ubuntu系统执行命令：




    
    
```sh
sudo apt-get install usb-creator-gtk usb-creator-common
```









# 挂载U盘





# 启动Startup Disk Creator工具




    
```sh
sudo usb-creator-gtk
```
    





# 制作启动盘





打开Startup Disk Creator工具之后，选择相应的ubuntu12.04镜像文件。  

之后，选择挂载好的U盘。点击“制作启动盘”即可。





# U盘启动安装





经过几分钟的等待之后，待启动盘制作成功之后，卸载U盘。  

在需要安装操作系统的电脑上，设置从U盘引导即可进入系统安装界面进行操作系统的安装。





# 参考资料





[Ubuntu官方说明1](http://www.ubuntu.com/download/help/create-a-usb-stick-on-ubuntu)





[Ubuntu官方说明2](https://help.ubuntu.com/community/Installation/FromUSBStick)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



