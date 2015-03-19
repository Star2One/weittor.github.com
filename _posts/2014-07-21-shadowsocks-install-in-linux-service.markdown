---
author: chris.hill
comments: true
date: 2014-07-21
excerpt: Linux配置shadowsocks
layout: post
slug: shadowsocks-install-in-linux-service
title: Linux配置shadowsocks
categories:
- it运维
- blog 
tags:
- Linux
---

* * *



shadowsocks是一个轻量级的sock5代理, 由python2.x写成.

项目地址:

<https://github.com/clowwindy/shadowsocks>


#1.安装

Debian / Ubuntu

```
apt-get install python-pip python-m2crypto
pip install shadowsocks
```
CentOS

```
yum install m2crypto python-setuptools
easy_install pip
pip install shadowsocks
```

#2.配置

创建/etc/shadowsocks/config.json文件作为配置文件, 以下是一个样例:

```
{
"server":"remote-shadowsocks-server-ip-addr",
"server_port":8883,
"local_address":"127.0.0.1",
"local_port":8883,
"password":"whosyourdaddy",
"timeout":300,
"method":"aes-256-cfb",
"fast_open":false,
"workers":1
}
```

#3.客户端

运行sslocal命令, 并指定配置文件即可

```
sslocal -c /etc/shadowsocks.json
```

*注意*
如果无法在运行时加载config.json文件, 可尝试手动运行:

```
sslocal -s 服务器地址 -p 服务器端口 -l 本地端端口 -k 密码 -m 加密方法
```

#4.服务端

*提示*
普通用户无需配置服务端. 详细配置信息以及服务端的配置请移步:

[Shadowsocks(简单中文) ](https://wiki.archlinux.org/index.php/Shadowsocks_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)

[Shadowsocks(官方Readme.md)](https://github.com/clowwindy/shadowsocks)

http://zqhong.github.io/2014/10/08/how-to-setting-shadowsocks-client/

http://www.v2ex.com/t/150064










* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

