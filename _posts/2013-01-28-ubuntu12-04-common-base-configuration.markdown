---
author: chris.hill
comments: true
date: 2013-01-28
excerpt: chris.hill近期详细学习了一下Ubuntu12.04的官方手册，现总结Ubuntu12.04系统常用一些基本配置如下，供参考。
layout: post
slug: ubuntu12-04-common-base-configuration
title: Ubuntu12.04常用基本配置
categories:
- IT运维
tags:
- Linux
---

* * *





_chris.hill_近期详细学习了一下Ubuntu12.04的官方手册，现总结Ubuntu12.04系统常用一些基本配置如下，供参考。





# 安装





## 软件包集合





查看当前的软件包集合





```sh
tasksel --list-tasks
```






查看软件包集合包含的软件





```sh
tasksel --task-packages dns-server
```





选择安装的软件包集合





```sh
sudo tasksel install dns-server
```




<!-- more -->

## 系统升级





推荐使用升级工具do-release-upgrade升级到稳定版本





```sh
do-release-upgrade
```





升级到开发版本





```sh
do-release-upgrade -d
```





## 安装RAID + LVM





在此不详细展开说明，请参考其他文档





## 安装Kernel Crash Dump工具





安装命令





```sh
sudo apt-get install linux-crashdump
```





## 确认配置





### 查看当前信息





```sh
cat /proc/cmdline 
```
内容如下：

```
BOOT_IMAGE=/vmlinuz-3.2.0-17-server   
root=/dev/mapper/PreciseS-root ro crashkernel=384M-2G:64M,2G-:128M
```





以上配置分别代表





* 内存大小小于384M时，不预留任何空间；  

* 当内存大小在384M到2G之间时，预留64M内存空间；  

* 当内存大小大于2G的时候，预留128M内存空间。






### 查看系统引导信息





```sh
dmesg | grep -i crash
```





## 测试配置





### 查看sysrq参数





```sh
cat /proc/sys/kernel/sysrq
```





若输出为0则需修改为1





```sh
sudo sysctl -w kernel.sysrq=1
```





之后切换到root用户执行中断命令：





```sh
echo c >/proc/sysrq-trigger
```





系统重启后，查看crash的相关镜像文件





```sh
ls /var/crash 
```
输出如下：

```
linux-image-3.0.0-12-server.0.crash
```





# 软件包管理





## dpkg





查看当前已经安装的软件包列表





```sh
dpkg -l
```





查看是否已经安装某个软件包





```sh
dpkg -l | grep apache2
```





查看某个软件包中包含的文件





```sh
dpkg -L ethtool
```





查看某个指定文件包含在哪个软件包





```sh
dpkg -S /etc/host.conf
```





安装软件包





```sh
sudo dpkg -i zip_3.0-4_i386.deb
```





删除软件包





```sh
sudo dpkg -r zip
```





## apt-get





安装软件包





```sh
sudo apt-get install nmap
```





删除软件包，保留配置文件：





```sh
sudo apt-get remove nmap
```





彻底删除软件包：





```sh
sudo apt-get --purge remove nmap
```





升级软件包列表：





```sh
sudo apt-get update
```





软件包更新：





```sh
sudo apt-get upgrade
```





apt-get日志：`/var/log/dpkg.log`





## aptitude





安装软件包





```sh
sudo aptitude install nmap
```





删除软件包：





```sh
sudo aptitude remove nmap
```





## 自动更新





### 安装自动更新软件包





```sh
sudo apt-get install unattended-upgrades
```





### 修改配置文件/etc/apt/apt.conf.d/50unattended-upgrades





指定自动更新列表





```
Unattended-Upgrade::Allowed-Origins
{"Ubuntu precise-security";
//      "Ubuntu precise-updates";
};
```





设置软件包更新黑名单，不更改指定的软件包列表





```sh
Unattended-Upgrade::Package-Blacklist
{
//      "vim";
//      "libc6";
//      "libc6-dev";
//      "libc6-i686";
};
```





### 修改配置文件/etc/apt/apt.conf.d/10periodic





指定软件包更新频率





```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```





### 查看cron配置/etc/cron.daily/apt





### 相关日志/var/log/unattended-upgrades





### 通知





可以在配置文件/etc/apt/apt.conf.d/50unattended-upgrades中设定Email通知。  

也可以通过安装apticron软件包。





```sh
sudo apt-get install apticron
```





```sh
vim /etc/apticron/apticron.conf
```
内容如下：

```
EMAIL="root@example.com"
```





## 软件包列表




```sh
/etc/apt/sources.list/etc/apt/sources.list.d
```




## 添加普通用户到sudo用户组





```sh
sudo adduser username sudo
```





# 网络配置





## 网卡配置





### 确认网卡信息查看网卡信息





```sh
ifconfig -a | grep eth
```





查看网卡详细信息





```sh
sudo lshw -class network
```





### 指定网卡名称配置文件




```sh
/etc/udev/rules.d/70-persistent-net.rules
```




### 设定网卡参数





安装配置工具





```sh
sudo apt-get install ethtool
```





查看当前网卡参数





```sh
sudo ethtool eth0
```







* 临时修改网卡参数





```sh
/sbin/ethtool -s eth0 speed 1000 duplex full
```







* 永久修改网卡参数





修改配置文件/etc/network/interfaces  

添加信息





```
auto eth0
iface eth0 inet static
pre-up /sbin/ethtool -s eth0 speed 1000 duplex full
```





## IP地址





### 临时ip地址





```sh
sudo ifconfig eth0 10.0.0.100 netmask 255.255.255.0
```





### 临时路由





```sh
sudo route add default gw 10.0.0.1 eth0
```





### 临时DNS设置





```sh
vim /etc/resolv.conf
```
输入内容：

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```





### 清除临时设置





```sh
sudo ip addr flush eth0
```





### DHCP设置





```sh
sudo vi /etc/network/interfaces
```


内容如下：


```
auto eth0
iface eth0 inet dhcp
```





### 静态ip地址配置





```sh
sudo vi /etc/network/interfaces
```




内容如下：

```
auto eth0
iface eth0 inet static
address 10.0.0.100
netmask 255.255.255.0
gateway 10.0.0.1
```





### Loopback端口设置





```sh
sudo vi /etc/network/interfaces
```



内容如下：

```
auto lo
iface lo inet loopback
```





## 域名解析





### DNS客户端配置





```sh
sudo vi /etc/network/interfaces
```




内容如下：

```
iface eth0 inet static
address 192.168.3.3
netmask 255.255.255.0
gateway 192.168.3.1
dns-search example.com sales.example.com dev.example.com
dns-nameservers 192.168.3.45 192.168.8.10
```





### 本地域名解析文件





```sh
sudo vi /etc/hosts
```





内容如下：

```
127.0.0.1 localhost
127.0.1.1 ubuntuserver
10.0.0.11 server1.example.com server1 vpn
10.0.0.12 server2.example.com server2 mail
10.0.0.13 server3.example.com server3 www
10.0.0.14 server4.example.com server4 file
```





### 域名服务解析顺序





```sh
sudo vi /etc/nsswitch.conf
```




内容如下：

```
hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4
```





解释：先查看/etc/hosts文件，再查询DNS服务





## 桥接设置





### 安装软件包





```sh
sudo apt-get install bridge-utils
```





### 配置桥接网卡





```sh
sudo vi /etc/network/interfaces
```
内容如下：





```
auto lo
iface lo inet loopback
auto br0
iface br0 inet static
address 192.168.0.10
network 192.168.0.0
netmask 255.255.255.0
broadcast 192.168.0.255
gateway 192.168.0.1
bridge_ports eth0
bridge_fd 9
bridge_hello 2
bridge_maxage 12
bridge_stp off
```





### 重启网络





```sh
sudo /etc/init.d/networking restart
```





Note：常用桥接配置命令brctl





## DHCP服务器配置





### DHCP服务能够提供的服务





列表如下  

* IP address and netmask  

* IP address of the default-gateway to use  

* IP adresses of the DNS servers to use  

* Host Name  

* Domain Name  

* Time Server  

* Print Server





### 安装





```sh
sudo apt-get install isc-dhcp-server
```





#### 配置





```
# minimal sample /etc/dhcp/dhcpd.confdefault-lease-time 600;
max-leasetime 7200;
subnet 192.168.1.0 netmask 255.255.255.0
{
range 192.168.1.150 192.168.1.200;
option routers 192.168.1.254;
option domain-name-servers 192.168.1.1, 192.168.1.2;
option domain-name "mydomain.example";
}
```





#### 重启服务





```sh
sudo /etc/init.d/isc-dhcp-server restart
```





## 时间同步服务NTP





### ntpdate





```sh
ntpdate -s ntp.ubuntu.com
```





* ntpd服务用途：与时间服务器进行同步






### 安装ntp服务





```sh
sudo apt-get install ntp
```





### 配置





```sh
vi  /etc/ntp.conf
```




内容如下：

```
ubuntu.pool.ntp.orgserver
ubuntu.pool.ntp.orgserver
ubuntu.pool.ntp.org
```






### 重启服务





```sh
sudo /etc/init.d/ntp reload
```





### 查看NTP服务状态





```sh
sudo ntpq -p
```





出现星号`*`时，才代表服务可用。





# 参考资料





Ubuntu12.04官方手册  

[https://help.ubuntu.com/12.04/index.html](https://help.ubuntu.com/12.04/index.html)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



