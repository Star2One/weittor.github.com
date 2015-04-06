---
author: chris.hill
comments: true
date: 2014-09-16
excerpt: Centos6配置ADSL
layout: post
slug: config-pppoe-in-centos6
title: Centos6配置ADSL 
categories:
- it运维
tags:
- Linux
---

* * *

Centos6配置ADSL

#环境
`系统`:Centos6

`软件`:rp-pppoe

#安装
```sh
yum install rp-pppoe
```

#配置
```sh
[root@localhost ~]# pppoe-setup ---------------#开始安装
Welcome to the PPPoE client setup. First, I will run some checks on
your system to make sure the PPPoE client is installed properly...
LOGIN NAME
Enter your Login Name (default root): hi12345 ---------------#输入上网账号
INTERFACE
Enter the Ethernet interface connected to the PPPoE modem
For Solaris, this is likely to be something like /dev/hme0.
For Linux, it will be ethX, where 'X' is a number.
(default eth0): ---------------#选择网卡,默认为eth0,如果使用无线网卡请输入wlan0
Do you want the link to come up on demand, or stay up continuously?
If you want it to come up on demand, enter the idle time in seconds
after which the link should be dropped. If you want the link to
stay up permanently, enter 'no' (two letters, lower-case.)
NOTE: Demand-activated links do not interact well with dynamic IP
addresses. You may have some problems with demand-activated links.
Enter the demand value (default no): ---------------#默认,按回车
DNS
Please enter the IP address of your ISP's primary DNS server.
If your ISP claims that 'the server will provide dynamic DNS addresses',
enter 'server' (all lower-case) here.
If you just press enter, I will assume you know what you areppp
doing and not modify your DNS setup.
Enter the DNS information here: ---------------#可手动输入DNS Server，默认为自动获得DNS
PASSWORD
Please enter your Password: ---------------#输入上网拨号密码
Please re-enter your Password: ---------------#输入上网拨号密码
USERCTRL
Please enter 'yes' (three letters, lower-case.) if you want to allow
normal user to start or stop DSL connection (default yes): ---------------#默认输入 yes
FIREWALLING
Please choose the firewall rules to use. Note that these rules are
very basic. You are strongly encouraged to use a more sophisticated NTHQB198529CFO
firewall setup; however, these will provide basic security. If you
are running any servers on your machine, you must choose 'NONE' and
set up firewalling yourself. Otherwise, the firewall rules will deny
access to all standard servers like Web, e-mail, ftp, etc. If you
are using SSH, the rules will block outgoing SSH connections which
allocate a privileged source port.
The firewall choices are:
0 - NONE: This script will not set any firewall rules. You are responsible
for ensuring the security of your machine. You are STRONGLY
recommended to use some kind of firewall rules.
1 - STANDALONE: Appropriate for a basic stand-alone web-surfing workstation
2 - MASQUERADE: Appropriate for a machine acting as an Internet gateway
for a LAN
Choose a type of firewall (0-2): 0 ---------------#防火墙配置，防火墙关闭，输入0
Start this connection at boot time
Do you want to start this connection at boot time?
Please enter no or yes (default no):y ---------------#是否自动启动拨号连接，输入y
* Summary of what you entered 
Ethernet Interface: eth0
User name: xy170638010214
Activate-on-demand: No
DNS addresses: Supplied by ISP's server
Firewalling: NONE
User Control: yes
Accept these settings and adjust configuration files (y/n)? y ---------------#确认信息无误，输入 y
Adjusting /etc/sysconfig/network-scripts/ifcfg-ppp0
Adjusting /etc/ppp/chap-secrets and /etc/ppp/pap-secrets
(But first backing it up to /etc/ppp/chap-secrets.bak)
(But first backing it up to /etc/ppp/pap-secrets.bak)
Congratulations, it should be all set up!
Type '/sbin/ifup ppp0' to bring up your xDSL link and '/sbin/ifdown ppp0'
to bring it down.
Type '/sbin/pppoe-status /etc/sysconfig/network-scripts/ifcfg-ppp0'
to see the link status.

```
#测试
```sh
[root@localhost ~]# pppoe-start ---------------#激活PPPOE，成功后会自动返回状态信息。不成功会报错
[root@localhost ~]# pppoe-status ---------------#查看PPPOE状态
pppoe-status: Link is up and running on interface ppp0
6: ppp0: mtu 1492 qdisc pfifo_fast state UNKNOWN qlen 3
link/ppp
inet 123.125.1.118 peer 113.14.32.1/32 scope global ppp0 ---------------获得的IP地址
[root@localhost ~]# ping baidu.com ----------------#ping baidu测试一下
```








* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

