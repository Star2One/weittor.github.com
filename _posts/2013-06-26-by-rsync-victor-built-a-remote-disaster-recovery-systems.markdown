---
author: chris.hill
comments: true
date: 2013-06-26 
excerpt: 通过rsync搭建远程容灾备份系统
layout: post
slug: by-rsync-victor-built-a-remote-disaster-recovery-systems
title: 通过rsync搭建远程容灾备份系统
categories:
- IT运维
tags:
- Linux
---

* * *





# 案例分析





    某网站为了保证数据安全，需要建立一个远程容灾系统，对Web服务器进行数据备份。  

    将网站数据在每天凌晨1点30分备份到远程的容灾服务器上。  

    由于数据量的影响，每天只进行增量备份。




## 系统环境





    WEB服务器 – LAMP 192.168.122.88  

    备份服务器 – LAMP-backup 192.168.122.89



## 解决方法





通过rsync的增量备份功能，配合Linux系统的crontab定时功能，实现数据的远程备份




<!-- more -->

# 配置过程





## 在LAMP主机上配置rsync服务端





### 安装rsync




    
    
```sh
yum install rsync -y
```
    





### 编辑配置文件/etc/rsyncd.conf





默认没有该文件，需要手动创建。





配置内容如下




    
```    
       uid = nobody    #守护进程的用户ID
       gid = nobody    #守护进程的用户组ID
       use chroot = no    #
       max connections = 10    #最大并发连接数量。默认为0，即没有限制
       strict modes = yes    #是否检查口令文件的权限。yes为检查口令文件权限，如果设置为yes，密码文件的权限必须为root用户权限
       pid file = /var/run/rsyncd.pid    #rsync守护进程对应的PID文件路径
       lock file = /var/run/rsync.lock    #用来指定支持max connections的锁文件
       log file = /var/log/rsyncd.log    #rsync的日志输出文件路径    
    
       [weittor]    #定义一个模块。weittor就是对应的模块名称
       path = /var/www/html    #指定需要备份的文件或目录，这里指定web网页目录/var/www/html
       comment = weittor web file    #模块的描述
       ignore errors    #表示可以忽略一些无关的IO错误
       read only = no    #设置为no表示客户端可以上传文件，设置为yes表示只读
       write only = no    #设置为no表示客户端可以下载文件，设置为yes表示不能下载
       hosts allow = *    #设置可以连接rsync服务器的主机，"*"表示允许连接任何主机
       hosts deny = 192.168.100.100    #设置禁止连接rsync服务器的主机地址
       list = false    #设定当用户请求可以使用的模块列表时，该模块是否被列出。设置为false，表示隐藏
       uid = root
       gid = root
       auth users =  backup    #定义可以连接该模块的用户名，多个用户可以用空格或逗号分割。
       secrets file = /etc/server.pass    #指定一个包含"用户名:密码"格式的文件，用户名就是auth users选项定义的用户，密码可以随意指定。该文件需要手动创建

```    





### 创建用户名有密码文件




    
    
```sh
vim /etc/server.pass
```
    





内容如下：

```
backup:weittor123
```




### 在LAMP主机上启动rsync守护进程




    
    
```sh
/bin/rsync --daemon
ps -ef|grep rsync
```





输出：


```    
    root      2374     1  0 13:58 ?        00:00:00 rsync –daemon  
```    





## 在LAMP-bakcup主机上配置rsync





### 安装rsync




    
    
```sh
yum install rsync -y
```





### 创建一个secrets file





此文件的内容为LAMP主机的rsyncd.conf文件中"auth users"选项指定用户的密码，文件名和路径可以随意指定。




    
    
```sh
vim /etc/LAMP.pass
```





内容如下：

```
weittor123
```



    
    
```sh
chmod 600 /etc/LAMP.pass
```





### 执行同步操作命令




    
    
```sh
rsync -vzrtopg --delete --progress --exclude "*access*" --exclude "debug*" backup@192.168.122.88::weittor /weittor.com --password-file=/etc/LAMP.pass
```





参数的含义如下

```
    -vzrtopg选项： 

        v是–verbose，即详细模式输出；  

        z表示–compress，即在传输时对备份文件进行压缩处理；  

        r表示–recrusive，也就是对子目录以递归模式处理；  

        t即–times，用户保持文件时间信息；  

        o即–owner，用来保持文件属主信息；  

        p即–perms，即用来保持文件权限；g即–group，用来保持文件属组信息  

    –delete选项：

        指定以rsync服务端为基准进行数据同步，保持rsync服务端目录与客户端目录完全一致  

    –progress选项：

        用于显示数据镜像同步的过程  

    –exclude选项：

        用于排除不需要传输的文件类型  

    backup@192.168.122.88::weittor选项：

        表示对服务器192.168.122.88的weittor模块进行备份  

    /weittor.com选项：

        指定备份文件在客户端机器上的存放路径  

    –password-file=/etc/LAMP.pass选项：

        用来指定客户机上存放密码文件的位置，使用该文件，在执行同步命令时就无需输入交互密码了
```

执行该命令即可将数据同步到LAMP-bakcup主机上





## 设置定时备份策略





由于rsync在执行数据备份时需要触发同步指令才能完成，而触发同步指令的方式有多种，我们这里将该指令放入crontab任务中任务中，定期执行备份策略。  

这种数据备份策略可以用于对数据安全性要求不高的业务系统中。  

根据要求，我们设置rsync每天1点30分执行镜像备份操作。




    
    
```sh
crontab -e
```
    





内容：




    
```sh 
30 00 * * * /usr/bin/rsync -vzrtopg –delete –progress –exclude "*access*" –exclude "debug*" backup@192.168.122.88::weittor /weittor.com –password-file=/etc/LAMP.pass    
```





至此，一个简单的rsync+crontab的远程备份系统就完成了





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



