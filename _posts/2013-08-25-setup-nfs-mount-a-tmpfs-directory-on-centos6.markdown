---
author: chris.hill
comments: true
date: 2013-08-25 
excerpt: chris.hii 将为大家介绍如何在centos6上通过NFS共享tmpfs目录
layout: post
slug: setup-nfs-mount-a-tmpfs-directory-on-centos6
title: 在centos6上通过NFS共享tmpfs目录
categories:
- IT运维
tags:
- Linux
---

* * *





  * Master: 192.168.1.100  

  * Client: 192.168.1.200







# 在Master服务器上配置tempfs文件系统





挂载tmpfs文件系统到/tmp/nfs目录，大小为1G




    
```sh
mount tmpfs /tmp/nfs -t tmpfs -o size=1G
```
    




<!-- more -->

# 在服务器上安装配置NFS





## 安装NFS 套件




    
```sh
yum install nfs-utils nfs-utils-lib
``` 





## 安装RPC服务




    
    
```sh
yum install rpcbind
```





## 设置开机启动




    
    
```sh
chkconfig nfs on
chkconfig rpcbind on
```





## 启动相关服务




    
    
```sh
service rpcbind start
```





    提示：之前的版本是启动portmap服务



    
    
```sh
service nfs start
rpcinfo -p localhost
```





查看进程信息





## 设置共享目录




    
    
```sh
vi /etc/exports
``` 



输入内容如下：
    
```    
    /tmp/nfs           192.168.1.200(rw,sync,no_root_squash,no_subtree_check,fsid=0)
``` 




格式说明

```
  #格式说明：[共享目录]   [第一台主机（权限）]  

  #fsid=N参数，是挂载tmpfs文件系统分区的关键，在这里设置为fsid=0，也可设置为其他数字
```
  
参数说明

```

    rw/ro    目录分享权限是可读写（read-write）或者只读（read-only），但最终能否读写还是与档案系统的rwx以及身份相关  

    sync/asyncsync      资料会同步写入内存和硬盘中, async 资料会先存入内存中，而不是直接写入硬盘。  

    no_root_squash/root_squash    客户端使用NFS的账号若为root时，系统如何判断这个账号的身份？默认情况下，客户端root的身份由root_squash设定缩减为nfsnobody，这样才能保证服务器系统的安全。但若想开放客户端的root身份来操作服务器档案系统，就需要设置no_root_squash  

    all_squash    无论登入NFS的客户端身份是什么，都会被缩减为匿名使用者，也就是nobody(nfsnobody)  

    anonuid/anongidanon    代表anonymous（匿名用户），前面关于*_squash提到的匿名用户的UID设定值，通常为nobody(nfsnobody)，但是你也可以自行设定这个UID的值！当然，这个UID必须存在你的/etc/passwd当中！anongid 则是群组的GID。  

    secure      限制客户端只能从小于1024的tcp/ip端口连接nfs服务器（默认设置）。  

    insecure      允许客户端从大于1024的tcp/ip端口连接服务器。  

    wdelay     检查是否有相关的写操作，如果有则将这些写操作 一起执行，这样可以提高效率（默认设置）。  

    no_wdelay      若有写操作则立即执行，应与sync配合使用。  

    subtree      若输出目录是一个子目录，则nfs服务器将检查其父目录的权限(默认设置)。  

    no_subtree      即使输出目录是一个子目录,nfs服务器也不检查其父目录的权限,这样可以提高效率。  

    fsid=N         对于tmpfs这种文件系统来说，进行NFS共享的话，需要在exports配置里加上fsid=0这个选项，在内核 2.6.17.1 增加了对使用NFS共享tmpfs的支持。

```





## 重新加载exports文件




    
    
```sh
exportfs -a
```





# 在客户端进行设置




    
    
```sh
yum install nfs-utils nfs-utils-lib
mkdir -p /mnt/nfs/home
```





查看Master主机发布的nfs目录




    
    
```sh
showmount -e 192.168.1.100
```





挂载服务器目录




    
    
```sh
mount  192.168.1.100 :/tmp/nfs /mnt/nfs/home
df -h
```





# 附录





## exportfs命令





exportfs命令是使得本地目录可以被NFS客户端进行安装，默认使用/etc/exports文件进行导出一个或者多个目录。




参数说明

``` 
    –r   重新读取etc/exports文件中的设置，并使设置生效，而无需重启NFS服务。  

    -v    显示当前共享目录及详细的选项设置。  

    -e   显示导出服务器上的所有文件系统。  

    -a   显示每个已安装在服务器上的所有网络文件系统 (NFS) 客户端和目录。  

    -d   显示当前装载 NFS 客户端的服务器上的所有目录。
 
    补充：

        用户端查询服务器分享资源的指令：/usr/sbin/showmount，这是另一个重要的NFS指令。  

        exportfs是用于在NFS Server端进行设置，查询的。而showmount则主要用于Client端，用于查看NFS分享出来的目录资源。
```



# 参考资料





[https://www.digitalocean.com/community/articles/how-to-set-up-an-nfs-mount-on-centos-6](https://www.digitalocean.com/community/articles/how-to-set-up-an-nfs-mount-on-centos-6)  

[http://liveforlinux.blog.51cto.com/3337218/935358](http://liveforlinux.blog.51cto.com/3337218/935358)  

[https://code.google.com/p/nfsroot/](https://code.google.com/p/nfsroot/)  

[http://zh.wikipedia.org/wiki/Tmpfs](http://zh.wikipedia.org/wiki/Tmpfs)  

[http://www.cnblogs.com/kaka/archive/2013/03/08/2950236.html](http://www.cnblogs.com/kaka/archive/2013/03/08/2950236.html)  

[http://www.liusuping.com/ubuntu-linux/Redhat-Linux-NFS-setting.html](http://www.liusuping.com/ubuntu-linux/Redhat-Linux-NFS-setting.html)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



