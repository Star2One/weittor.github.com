---
author: chris.hill
comments: true
date: 2014-11-10
excerpt: 通过http方式检测varnish状态
layout: post
slug: check-varnish-status-by-http-in-nginx
title: 通过http方式检测varnish状态
categories:
- web应用
tags:
- Nginx
---

* * *

#需求
修改nginx后端varnish的upstream检测方式为http的检测方式

注：采用了淘宝的Tengine替代官方版Nginx

#修改方法
修改upstream_varnish.conf配置文件

```nginx
    upstream    cluster_varnish{
        server 192.168.10.11:900;
        server 192.168.10.12:900;
        hash $request_uri;
        hash_again 1;
        check interval=3000 rise=2 fall=5 timeout=1000 type=http;    <<配置
        check_http_send "HEAD / HTTP/1.0\r\n\r\n";    <<配置
        check_http_expect_alive http_2xx http_3xx;    <<配置
    }
```
##说明

1)check指令后面的参数意义

`interval`：向后端发送的健康检查包的间隔。

`fall(fall_count)`: 如果连续失败次数达到fall_count，服务器就被认为是down。

`rise(rise_count)`: 如果连续成功次数达到rise_count，服务器就被认为是up。

`timeout`: 后端健康请求的超时时间。

`default_down`: 设定初始时服务器的状态，如果是true，就说明默认是down的，如果是false，就是up的。默认值是true，也就是一开始服务器认为是不可用，要等健康检查包达到一定成功次数以后才会被认为是健康的。
 
`type`：健康检查包的类型，现在支持以下多种类型

* tcp：简单的tcp连接，如果连接成功，就说明后端正常。
* ssl_hello：发送一个初始的SSL hello包并接受服务器的SSL hello包。
* http：发送HTTP请求，通过后端的回复包的状态来判断后端是否存活。
* mysql: 向mysql服务器连接，通过接收服务器的greeting包来判断后端是否存活。
* ajp：向后端发送AJP协议的Cping包，通过接收Cpong包来判断后端是否存活。
 
`port`: 指定后端服务器的检查端口。
可以指定不同于真实服务的后端服务器的端口，比如后端提供的是443端口的应用，你可以去检查80端口的状态来判断后端健康状况。默认是0，表示跟后端server提供真实服务的端口一样。
 
2)`check_http_send http_packet`:
该指令可以配置http健康检查包发送的请求内容。为了减少传输数据量，推荐采用`HEAD`方法。

当采用长连接进行健康检查时，需在该指令中添加keep-alive请求头，如："HEAD / HTTP/1.1\r\nConnection: keep-alive\r\n\r\n"。 同时，在采用"GET"方法的情况下，请求uri的size不宜过大，确保可以在1个interval内传输完成，否则会被健康检查模块视为后端服务器或网络异常。
 
3)check_http_expect_alive: 
该指令指定HTTP回复的成功状态，默认认为2XX和3XX的状态是健康的。
 
4)check_status: 
显示服务器的健康状态页面。该指令需要在http块中配置。

在Tengine-1.4.0以后，你可以配置显示页面的格式。支持的格式有: `html`、`csv`、 `json`。默认类型是html。

你也可以通过请求的参数来指定格式，假设`/status`是你状态页面的URL， format参数改变页面的格式，比如：
```
/status?format=html
/status?format=csv
/status?format=jsonfan
```

#参考资料
http://tengine.taobao.org/document/http_upstream_check.html

http://www.linuxidc.com/Linux/2014-03/98779.htm


* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

