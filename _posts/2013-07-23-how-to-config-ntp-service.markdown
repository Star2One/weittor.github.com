---
author: chris.hill
comments: true
date: 2013-07-23
excerpt: ntp时间同步常用操作
layout: post
slug: python-package-manager-list
title: ntp时间同步常用操作 
categories:
- IT运维
tags:
- Linux
---

* * *



#named.ca
下载地址

<ftp://ftp.rs.internic.net/domain/named.root>

#centos配置时间同步
安装软件包

```sh
yum install ntp ntpdate ntp-doc
```

##ntpdate同步

```sh
ntpdate pool.ntp.org
```
##ntp同步
编辑ntp.conf

```
server 0.rhel.pool.ntp.org
server 1.rhel.pool.ntp.org
server 2.rhel.pool.ntp.org
```

启动ntp服务

```sh
/etc/init.d/ntpd start
chkconfig ntpd on
```

#常用时间服务器
centos时间同步服务器

```
server 0.centos.pool.ntp.org
server 1.centos.pool.ntp.org
server 2.centos.pool.ntp.org
```
redhat时间同步服务器

```
server 0.rhel.pool.ntp.org
server 1.rhel.pool.ntp.org
server 2.rhel.pool.ntp.org
```
亚洲时间同步服务器

```
server 0.asia.pool.ntp.org
server 1.asia.pool.ntp.org
server 2.asia.pool.ntp.org
server 3.asia.pool.ntp.org
```

ntp.org时间服务列表

```
http://support.ntp.org/bin/view/Servers/StratumOneTimeServers
http://www.pool.ntp.org/zone/asia
```

#参考资料

<http://www.cyberciti.biz/faq/howto-install-ntp-to-synchronize-server-clock/>

<http://docs.oracle.com/cd/E19683-01/806-4077/dnsintro-102/index.html>



* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

