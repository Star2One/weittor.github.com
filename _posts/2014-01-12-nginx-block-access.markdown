---
author: chris.hill
comments: true
date: 2014-01-12
excerpt: nginx屏蔽指定访问的方法
layout: post
slug: nginx-block-access
title: nginx屏蔽指定访问的方法
categories:
- web应用
tags:
- Nginx
---

* * *

nginx屏蔽指定访问的方法


Nginx可以根据用户访问的特征，对特定的访问匹配进行屏蔽或者限制。

目前情况，可以用于限制的屏蔽特征包括如下几种：

* User Agent
* IP地址
* Referrer
* 网段
* GeoIP

#基于User Agent进行限制

```sh
if ($http_user_agent ~ "Windows 95|Windows 98|biz360.com|xpymep|TurnitinBot|sindice|Purebot|libwww-perl")  {                                                               
  return 403;                                                                                                                                                                                    
  break;                                                                                                                                                                                         
}
```


#基于IP地址或IP网段进行限制
编辑nginx.conf

```nginx
include blockips.conf;        #block ip配置文件
```
编辑blockips.conf配置

1）基于IP地址限制

```nginx
deny 85.17.26.68;     
deny 85.17.230.23;   
deny 173.234.11.105; 
deny 173.234.31.9;    
deny 173.234.38.25;   
deny 173.234.153.30;  
deny 173.234.153.106;
deny 173.234.175.68; 
deny 190.152.223.27;  
deny 195.191.54.90;   
deny 195.229.241.174; 
deny 210.212.194.60; 
```

2）基于网段进行限制

```nginx
deny 69.28.58.0/24;   
deny 79.142.64.0/20;  
deny 80.67.0.0/20;    
deny 88.214.193.0/24; 
```


需要重新加载nginx配置



#基于Referrer进行限制

```nginx
if ($http_referer ~* (viagra|cialis|levitra) ) {
  return 403;
}
```
需要重新加载nginx配置





#基于GeoIP进行限制
限制某个国家所有用户访问，可以通过GeoIP进行限制。

1）下载GeoIP文件
地址：<https://www.maxmind.com>

2）nginx编译时需要启用GeoIP支持功能

3）GeoIP配置
启用GeoIP配置文件

```nginx
geoip_country /etc/nginx/GeoIP.dat;
```
配置限制指定国家的访问

```nginx
if ($geoip_country_code ~ (BR|CN|KR|RU) ) {
  return 403;
}
```
需要重新加载nginx配置


#参考资料
<http://eng.eelcowesemann.nl/linux-unix-android/nginx/nginx-blocking/>

<http://www.cyberciti.biz/faq/setting-up-nginx-to-redirect-mobile-users-to-subdomain/>










* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

