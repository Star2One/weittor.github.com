---
author: chris.hill
comments: true
date: 2013-06-29
excerpt: 本文将介绍如何通过rsync+inotify搭建实时远程备份服务器。
layout: post
slug: build-a-real-time-backup-system
title: 通过rsync+inotify搭建实时备份系统
categories:
- IT运维
tags:
- Linux
---

* * *





# rsync介绍





## 优点





安全性高、备份迅速、支持增量备份。  

rsync可以解决对实时性要求不高的系统数据备份需求。  

例如定期的备份文件服务器数据到远端服务器，对本地磁盘定期做数据镜像等。





## 不足





1) rsync同步数据时，需要扫描所有文件后进行比对，进行差量传输。  

如果文件数量达到了百万甚至千万量级，扫描所有文件将是非常耗时的。  

而且正在发生变化的往往是其中很少的一部分，这是非常低效的方式。  

2) rsync不能实时的去监测、同步数据。  

虽然它可以通过linux守护进程的方式进行触发同步，但是两次触发动作一定会有时间差。  

这样就导致了服务端和客户端数据可能出现不一致，无法在应用故障时完全的恢复数据。





# inotify介绍





inotify 是一种强大的、细粒度的、异步的文件系统事件监控机制。  

通过Inotify可以监控文件系统中添加、删除，修改、移动等各种细微事件，利用这个内核接口，第三方软件就可以监控文件系统下文件的各种变化情况，而inotify-tools就是这样的一个第三方软件。





* 原理

inotify可以监控文件系统的各种变化，当文件有任何变动时，就触发rsync同步，通过rsync+inotify组合可以解决同步数据的实时性问题。





# 应用案例





## 案例分析





通过rsync+inotify将web服务器的网页文件实时备份到远程备份服务器上。





  * 系统环境

    * WEB服务器 – LAMP 192.168.122.88  

    * 备份服务器 – LAMP-backup 192.168.122.89






## 安装配置





### 在LAMP节点上安装inotify工具inotify-tools





#### 确定内核支持inotify特性





1)内核达到2.6.13版本以上




    
    
```sh
uname -r
```
    





输出：`2.6.32-220.el6.x86_64`





2)判断内核是否编译支持inotify特性




    
    
```sh
ll /proc/sys/fs/inotify
```





输出：




    
```    
    总用量 0
       -rw-r–r– 1 root root 0 6月  29 15:07 max_queued_events
       -rw-r–r– 1 root root 0 6月  29 15:07 max_user_instances
       -rw-r–r– 1 root root 0 6月  29 15:07 max_user_watches
```    





如果有上面三项输出，表示系统已经默认支持inotify，接着就可以开始安装inotify-tools了。



<!-- more -->


#### 下载inotify-tools工具





[http://inotify-tools.sourceforge.net](http://inotify-tools.sourceforge.net)





#### 编译安装




    
    
```sh
tar zxvf inotify-tools-3.14.tar.gz 
cd inotify-tools-3.14
./configure
make
make install
```





确认安装成功




    
    
```sh
ll /usr/local/bin/inotifywa*
```





输出:




    
```    
    -rwxr-xr-x 1 root root 38630 6月  26 14:22 /usr/local/bin/inotifywait
    -rwxr-xr-x 1 root root 40401 6月  26 14:22 /usr/local/bin/inotifywatch
``` 





inotify-tools安装完成后，会生成inotifywait和inotifywatch两个指令，其中，inotifywait用于等待文件或文件集上的一个特定事件，它可以监控任何文件和目录设置，并且可以递归地监控整个目录树。  

inotifywatch用于收集被监控的文件系统统计数据，包括每个inotify事件发生多少次等信息。





### 在LAMP节点和LAMP-bakcup节点安装rsync




    
    
```sh
yum install rsync
```





### 在LAMP-backup节点配置rsync




    
    
```sh
cat /etc/rsyncd.conf
```





内容如下




    
```    
    uid = nobody
    gid = nobody
    use chroot = no
    max connections = 10
    strict modes = yes
    pid file = /var/run/rsyncd.pid
    lock file = /var/run/rsync.lock
    log file = /var/log/rsyncd.log
    [weittor-backup]
    path = /weittor-backup
    comment = weittor web file
    ignore errors
    read only = no
    write only = no
    hosts allow = *
    hosts deny = 192.168.100.100
    list = false
    uid = root
    gid = root
    auth users =  backup
    secrets file = /etc/server.pass
```    





创建密码验证文件




    
    
```sh
vim /etc/server.pass
```





内容如下：

```
backup:weittor123
```




启动rsync守护进程，接着将rsync服务加入到自启动文件中：




    
    
```sh
echo  "/usr/bin/rsync --daemon" >>/etc/rc.local
```





### 配置LAMP节点





配置内容发布节点的主要就是将LAMP节点的/var/www/html网页目录的内容实时同步到LAMP-backup服务节点上可以通过一个脚本实现该功能




    
    
```sh
cat /etc/init.d/inotify-rsync.sh
```





内容如下：




    
    
```sh
    #!/bin/bash
    host1=192.168.122.89
    src=/var/www/html/
    dst1=weittor-backup
    user1=backup
                  
    /usr/local/bin/inotifywait -mrq --timefmt '%d/%m/%y %H:%M' --format '%T %w%f%e' -e close_write,delete,create,attrib $src \
    | while read files
      do
      /usr/bin/rsync -vzrtopg --delete --progress  $src $user1@$host1::$dst1 --password-file=/etc/weittor-backup.pass
      echo "${files} was rsynced" >>/tmp/rsync.log 2>&1
     done
```
     





脚本解释如下：


```
    –timefmt：指定时间的输出格式  

    –format：指定变化文件的详细信息
```







这两个参数一般配合使用，通过指定输出格式，输出类似与：




    
```    
    26/06/13 14:35 /var/www/html/date.txtCREATE was rsynced
    26/06/13 14:35 /var/www/html/date.txtCLOSE_WRITE,CLOSE was rsynced
    26/06/13 14:39 /var/www/html/date.txtCLOSE_WRITE,CLOSE was rsynced
    26/06/13 14:42 /var/www/html/date.txtCLOSE_WRITE,CLOSE was rsynced
    26/06/13 14:43 /var/www/html/date.txtCLOSE_WRITE,CLOSE was rsynced
```    





该脚本通过inotify监控文件目录的变化，进而触发rsync进行同步操作，由于这个过程是一种主动触发操作，通过系统内核完成的，所以，比起那些遍历整个目录的扫描方式，效率要高很多





接着，创建用户名密码验证文件




    
    
```sh
vim /etc/weittor-backup.pass
```





内容如下：weittor123





将该脚本放到/etc/init.d/目录下，然后给定可执行权限，放到后台运行：




    
    
```sh
chmod 755 /etc/init.d/inotify-rsync.sh 
/etc/init.d/inotify-rsync.sh  &
```





最后，将此脚本加入系统自启动文件：




    
    
```sh
echo  "/etc/init.d/inotify-rsync.sh  >>/etc/rc.local
```





这样就完成了内容发布节点的所有配置工作





### 测试rsync+inotify实时同步功能





所有配置完成后，可以在网页发布节点的/var/www/html/目录下添加、删除或者修改某个文件，然后到LAMP-backup服务节点对应的目录查看文件是否跟随网页发布节点的/var/www/html目录下文件发生变化，如果你看到LAMP-backup节点对应的目录文件跟着LAMP节点目录文件同步变化，那么我们这个业务系统就配置成功了。  

比如：  

在LAMP节点执行




    
    
```sh
date >>/var/www/html/date.txt
```





在LAMP-backup节点查看该文件是否正常同步




    
    
```sh
ls /weittor-bakcup
```





# 参考资料





[http://ixdba.blog.51cto.com/2895551/580280](http://ixdba.blog.51cto.com/2895551/580280)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



