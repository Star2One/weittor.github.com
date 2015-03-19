---
author: chris.hill
comments: true
date: 2013-09-09
excerpt: 教你如何在centos系统上实现bonding网卡绑定功能。
layout: post
slug: network-interface-bonding-on-centos-system
title: centos系统配置bonding
categories:
- IT运维
tags:
- Linux
---

* * *





# 准备工作





## 系统信息





  * 系统：centos  

  * 网卡：至少2块网卡





## 确定网卡MAC地址




    
  
```sh  
ifconfig eth0 | grep HWaddr
ifconfig eth1 | grep HWaddr
```
    





# 编辑网卡配置文件





## 编辑虚拟网卡的配置文件




    
    
```sh
cd /etc/sysconfig/network-scripts
```    




<!-- more -->
    
    
```sh
vi ifcfg-bond0
```    

内容如下：



    
``` 
DEVICE=bond0
IPADDR=192.168.1.101
NETMASK=255.255.255.0
ONBOOT=yes
```    





可以在/etc/sysconfig/network 文件中设定默认网关地址


内容如下：

    
```  
GATEWAY=192.168.1.1
``` 





## 备份网卡的配置文件




    
    
```sh
cd /etc/sysconfig/network-scripts
mv ifcfg-eth0 OLD.ifcfg-eth0
mv ifcfg-eth1 OLD.ifcfg-eth1
```





## 创建新的网卡配置文件





网卡eth0的配置文件




    
    
```sh
cd /etc/sysconfig/network-scripts
vi ifcfg-eth0
```




内容如下：
    
```    
DEVICE=eth0
MASTER=bond0
SLAVE=yes
ONBOOT=yes
```    





网卡eth1的配置文件




    
    
```sh
cd /etc/sysconfig/network-scripts
vi ifcfg-eth1
``` 




内容如下：
    
```    
DEVICE=eth1
MASTER=bond0
SLAVE=yes
ONBOOT=yes
```    





# 添加bonding模块





## 添加1块bonding网卡





编辑配置文件




    
    
```sh
vi /etc/modprobe.conf
```




内容如下：    
    
```
alias bond0 bonding
options bond0 miimon=100 mode=1
```    





参数说明：
mode指定网卡bonding的运行模式（共0-6这7种模式），这里我们设置mode=1，主备模式
常用的模式有：

  * mode=0    负载均衡模式(balance-rr) Round-robin policy（平衡抡循环策略）  

  * mode=1    主备模式 (active-backup) Active-backup policy（主-备份策略）  

  * mode=6    负载均衡模式(balance-alb) Adaptive load balancing（适配器适应性负载均衡）  

  * miimon=100参数设置表示每100毫秒检测运行链路的状态



## 添加2块bonding网卡





编辑配置文件




    
    
```sh
vi /etc/modprobe.conf
```



内容如下:
    

```    
alias bond0 bonding
alias bond1 bonding
options bond0 miimon=100 mode=1 max_bonds=2
```    





参数说明：



  * max_bonds=2   如果需要设定多个bonding网卡的话，需要设置该参数，其中N等于bonding网卡的数量




# 重启网络




    
    
```sh
service network restart
```





## 查看bonding模块是否挂载成功




    
    
```sh
lsmod | grep bond
```





## 查看网卡状态




    
    
```sh
ifconfig bond0
cat /proc/net/bonding/bond0
```





# 修改主备模式下bonding网卡的状态





在主备模式下，可以通过命令设置当前激活网卡





## 查看当前激活的网卡




    
    
```sh
cat /proc/net/bonding/bond0
```




输出内容如下：    
    
```
    Ethernet Channel Bonding Driver: v3.4.0 (October 7, 2008)
    Bonding Mode: fault-tolerance (active-backup)
    Primary Slave: None
    Currently Active Slave: eth0
    MII Status: up
    MII Polling Interval (ms): 100
    Up Delay (ms): 0
    Down Delay (ms): 0
    Slave Interface: eth0
    MII Status: up
    Link Failure Count: 0
    Permanent HW addr: 00:0c:29:4c:79:5e
    Slave Interface: eth1
    MII Status: up
    Link Failure Count: 0
    Permanent HW addr: 00:0c:29:4c:79:68
```    





可知当前激活的网卡为eth0





执行ifenslave命令设置激活网卡为eth1




    
    
```sh
ifenslave -c bond0 eth1 eth0
```





查看bond0网卡状态




    
    
```sh
cat /proc/net/bonding/bond0
```




输出内容如下：    
    
```
    Ethernet Channel Bonding Driver: v3.4.0 (October 7, 2008)
    Bonding Mode: fault-tolerance (active-backup)
    Primary Slave: None
    Currently Active Slave: eth1
    MII Status: up
    MII Polling Interval (ms): 100
    Up Delay (ms): 0
    Down Delay (ms): 0
    Slave Interface: eth0
    MII Status: up
    Link Failure Count: 0
    Permanent HW addr: 00:0c:29:4c:79:5e
    Slave Interface: eth1
    MII Status: up
    Link Failure Count: 0
    Permanent HW addr: 00:0c:29:4c:79:68
```    





可知当前激活的网卡已修改为eth1





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



