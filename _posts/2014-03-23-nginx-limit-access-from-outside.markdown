---
author: chris.hill
comments: true
date: 2014-03-23
excerpt: nginx只允许内网访问
layout: post
slug: nginx-limit-access-from-outside
title: nginx只允许内网访问
categories:
- web应用
tags:
- Nginx
---

* * *

#需求
配置`blog.weittor.com/admin`这个链接在外网不允许访问，只能通过内网访问这个链接。  

#配置
编辑配置文件vhost_blog.weittor.com

添加如下配置

```nginx
        location ~ ^/admin/ {
                proxy_pass  http://tomcat_admin_cluster;
                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                allow 192.168.122.0/24;
                deny all;
        }
```

测试配置

```bash
nginx -t -c nginx.conf
```

加载配置

```bash
nginx  -c nginx.conf -s reload
```



* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)


