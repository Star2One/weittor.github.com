---
author: chris.hill
comments: true
date: 2012-10-18 13:27:10+00:00
excerpt: 介绍一下Ubuntu Server 12.04如何切换到英文环境
layout: post
slug: ubuntu-server-12-04-switch-to-english-environment
title: Ubuntu Server 12.04切换到英文环境
categories:
- IT运维
tags:
- Linux
---

* * *





# 背景





安装Ubuntu Server 12.04的时候，选择了中文环境进行安装，如何切换回纯英文的界面呢？



<!-- more -->


# 方法





修改/var/lib/locales/supported.d/local文件




    
    
```sh
vi /var/lib/locales/supported.d/local
```
内容如下：

```
en_US.UTF-8 UTF-8
```

    





重新生成新的locale文件




    
    
```sh
locale-gen –purge
```
    





修改 /etc/default/locale文件




    
      
```sh 
vi /etc/default/locale 
```
内容如下：

```
LANG="en_US.UTF-8" 
LANGUAGE="en_US:en" 
LC_ALL="en_US.UTF-8"
```
    





重启系统




    

```sh    
init 6
```    





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



