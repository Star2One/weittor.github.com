---
author: chris.hill
comments: true
date: 2013-12-22
excerpt: chris hill办公环境的宿主机使用的是办公室的DHCP网络，需要配置VirtualBox虚拟机使其能够通过办公室的DHCP网络获取IP地址，从而达到能够同时访问内网和外网的目的。
layout: post
slug: configuring-virtualbox-in-a-dhcp-environment-network
title: 在DHCP环境下配置VirtualBox虚拟机网络
categories:
- 云计算与虚拟化
tags:
- 虚拟化
---

* * *





# 需求





_chris.hill_办公环境的宿主机使用的是办公室的_DHCP_网络，需要配置VirtualBox虚拟机使其能够通过办公室的_DHCP_网络获取_IP地址_，从而达到能够同时访问内网和外网的目的。





以下是简要的配置步骤。





# 步骤







1.打开VirtualBox虚拟机网络配置界面

![](/images/2013year/2013-12-22_how-to-configure-the-virtualbox-network-during-dhcp-env01.png)



<!-- more -->


2.点击左侧的"网络"选项卡
编辑第一块网络的"连接方式"为"桥接网卡"
并将"界面名称"选择为"第一块物理网卡的名字"

![](/images/2013year/2013-12-22_how-to-configure-the-virtualbox-network-during-dhcp-env02.png)



3.保存退出
在虚拟机内设置IP地址为DHCP自动获取的方式，并重启网络配置，即可获取相应的IP配置信息。






* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



