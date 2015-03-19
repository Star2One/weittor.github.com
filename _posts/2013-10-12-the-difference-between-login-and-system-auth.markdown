---
author: chris.hill
comments: true
date: 2013-10-12 
excerpt: 探究一下/etc/pam.d/login和/etc/pam.d/system-auth配置文件的区别与联系
layout: post
slug: the-difference-between-login-and-system-auth
title: /etc/pam.d/login和/etc/pam.d/system-auth区别与联系
categories:
- IT运维
tags:
- Linux
---

* * *





最近_chris.hill_在读IBM REDBOOK[Linux Performance and Tuning Guidelines]时，提到有关修改最大文件打开数修改的方法，其中有这么一段文字





    "
    This command can be used, for example, to limit Oracle® users on the fly. To set it on startup,
    enter the following lines, for example, in /etc/security/limits.conf:
    soft nofile 4096
    hard nofile 10240
    In addition, make sure that the default pam configuration file (/etc/pam.d/system-auth  for
    Red Hat Enterprise Linux, /etc/pam.d/common-session  for SUSE Linux Enterprise Server)
    has the following entry:
    session required pam_limits.so
    This entry is required so that the system can enforce these limits.
    "



由于过去_chris hill_都是直接修改的_/etc/pam.d/login_与_/etc/pam.d/sshd_文件，而该书里面说的方法是修改_/etc/pam.d/system-auth_文件，因此对这三个文件的区别进行了探究。





现将内容记录如下，供参考。

<!-- more -->





#/etc/pam.d目录  

存放着与PAM模块有关安全策略配置文件
在修改资源限制的过程中，主要会涉及到如下三个文件







  * etc/pam.d/system-auth文件  

redhat系统及其衍生版系统将通用的安全策略配置统一放置在该文件。其他安全策略配置文件可以include或require该文件。该文件作为一个全局配置文件存在。


  * /etc/pam.d/login文件

该文件主要对通过本地终端登录的用户执行安全策略，其引用了system-auth配置。


  * /etc.pam.d/sshd文件  

该文件主要对通过SSH远程登录的用户执行安全策略，其引用了system-auth配置。





在修改最大文件打开数的配置过程中，需要修改安全策略，加载pam_limits.so模块。





我们只需要修改/etc/pam.d/system-auth 文件即可


```
session required pam_limits.so
```





默认地，redhat系统(centos系统)的/etc/pam.d/login和/etc.pam.d/sshd都有如下一行配置

```
session    include      system-auth
```





从而保证了system-auth中的内容可以完整地被其他的两个文件所引用，并顺利地执行。





_提示_：SUSE系统的全局安全策略配置文件为/etc/pam.d/common-session





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



