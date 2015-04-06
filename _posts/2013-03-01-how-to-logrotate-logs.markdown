---
author: chris.hill
comments: true
date: 2013-03-01
excerpt: 两种日志轮转的方法
layout: post
slug: how-to-logrotate-logs
title: 两种日志轮转的方法 
categories:
- it运维
tags:
- Linux
---

* * *



#方法1
介绍：
通过find进行查找删除过期的日志。

添加crontab定时任务

```sh
06 06 * * * bash -c "ionice -n3 find /data/logs/*/tomcat_*.log -type f -mtime +30 -delete  >>/tmp/delete.log 2>&1"

```

#方法2
介绍：
通过logrotate进行日志轮转


crontab规则

```sh
59 23 * * * /usr/sbin/logrotate -f -v /data/scripts/logrotate_tomcat.conf
```

`/data/scripts/logrotate_tomcat.conf`内容如下

```
/data/logs/*/tomcat_*.log {
     missingok
     ifempty
     compress
     rotate 7
     daily
     dateext
     copytruncate
}
```





* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

