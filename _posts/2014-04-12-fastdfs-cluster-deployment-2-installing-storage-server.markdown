---
author: chris.hill
comments: true
date: 2014-04-12
excerpt: "FastDFS是一个开源的轻量级分布式文件系统。\nFastDFS服务端有两个角色：跟踪器（tracker server）和存储节点（storage\
  \ server）。 \n跟踪器主要起调度工作，在访问上起负载均衡的作用。存储节点用于存储数据，直接向前端服务提供数据。   \n本文chris.hill将通过实例为大家介绍有关FastDFS\
  \ Storagge Server的搭建过程。"
layout: post
slug: fastdfs-cluster-deployment-2-installing-storage-server
title: FastDFS集群安装部署[2]:安装Storage Server
categories:
- 网络存储应用
tags:
- FastDFS
---

* * *





# FastDFS+Nginx图片存储方案





服务器IP规划






    FastDFS storage server01    192.168.122.115  

    FastDFS storage server02    192.168.122.116  

    FastDFS tracker server01    192.168.122.113  

    FastDFS client    192.168.122.120







架构图与Tracker Server的安装步骤请参考博文

[FastDFS集群安装部署[1]:安装Tracker Server](http://www.weittor.com/fastdfs-cluster-deployment-1-installing-a-tracker-server.html)





有关的tracker server的端口号信息如下





    192.168.122.113:22122






接下来，_chris.hill_将为大家介绍FastDFS Storage Server的搭建过程。





# 部属FastDFS Tracker Server





以下操作在`FastDFS storage server01    192.168.122.115`服务器上进行



<!-- more -->



## 安装依赖软件包




    
```sh
yum install -y wget gcc make
```





## 安装libevent网络库





FastDFS依赖开源网络库libevent，因此先安装它。  

libevent的官方站点：[http://libevent.org/](http://libevent.org/)  

下载最新版2.0.21版本。




    
```sh
mkdir install   
cd install
wget "https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz"
tar zxf libevent-2.0.21-stable.tar.gz
cd libevent-2.0.21-stable
./configure --prefix=/usr  #编译
make    
make install    #安装
ldconfig    #加载更新动态库
```





## 安装FastDFS Storage Server





下载地址：(http://code.google.com/p/fastdfs/)




    
```sh
cd ～/install/
wget http://fastdfs.googlecode.com/files/FastDFS_v4.06.tar.gz
tar xvzf FastDFS_v4.06.tar.gz
cd FastDFS
ls
```





编辑安装配置文件make.sh




    
```sh
vim make.sh 
##取消WITH_LINUX_SERVICE=1选项前面的注释
```





执行安装




    
```sh
./make.sh
./make.sh install
```





查看fdfs进程启动选项




    
```sh
chkconfig --list | grep fdfs
#输出
fdfs_storaged   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
fdfs_trackerd   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
```





## 配置 FastDFS Storage Server





配置FastDFS Storage Server，需要修改 storage.conf 配置文件




    
```sh
vim /etc/fdfs/storage.conf
```





修改如下内容




    
```
    ....... 
    # the base path to store data and log files
    base_path=/var/fdfs     ##配置主目录
     
    # store_path#, based 0, if store_path0 not exists, it's value is base_path
    # the paths must be exist
    #store_path0=/home/yuqing/fastdfs
    #store_path1=/home/yuqing/fastdfs2
    store_path0=/storage/data1      ##数据原文件目录
    
    # tracker_server can ocur more than once, and tracker_server format is
    #  "host:port", host can be hostname or ip address
    #tracker_server=192.168.209.121:22122
    tracker_server=192.168.122.113:22122    ##tracker server的端口信息
    ......
```





需要创建FastDFS存储目录，否则无法启动




    
```sh 
mkdir -p /var/fdfs
mkdir -p /storage/data1
```





启动 FastDFS Storage Server




    
```sh 
service fdfs_storaged start
```





输出信息如下，可以看到创建目录的详细过程




    
```
    data path: /storage/data1/data, mkdir sub dir...
    mkdir data path: 00 ...
    mkdir data path: 01 ...
    mkdir data path: 02 ...
    mkdir data path: 03 ...
    mkdir data path: 04 ...
    mkdir data path: 05 ...
    mkdir data path: 06 ...
    mkdir data path: 07 ...
    mkdir data path: 08 ...
    mkdir data path: 09 ...
    mkdir data path: 0A ...
    mkdir data path: 0B ...
    ........
    mkdir data path: BB ...
    mkdir data path: BC ...
    mkdir data path: BD ...
    mkdir data path: BE ...
    mkdir data path: BF ...
    mkdir data path: C0 ...
    mkdir data path: C1 ...
    mkdir data path: C2 ...
    mkdir data path: C3 ...
    mkdir data path: C4 ...
    mkdir data path: C5 ...
    mkdir data path: C6 ...
    mkdir data path: C7 ...
    mkdir data path: C8 ...
    mkdir data path: C9 ...
    ......
    mkdir data path: DD ...
    mkdir data path: DE ...
    mkdir data path: DF ...
    mkdir data path: E0 ...
    mkdir data path: E1 ...
    mkdir data path: E2 ...
    mkdir data path: E3 ...
    mkdir data path: E4 ...
    mkdir data path: E5 ...
    mkdir data path: E6 ...
    mkdir data path: E7 ...
    mkdir data path: E8 ...
    mkdir data path: E9 ...
    mkdir data path: EA ...
    mkdir data path: EB ...
    .......
    mkdir data path: FB ...
    mkdir data path: FC ...
    mkdir data path: FD ...
    mkdir data path: FE ...
    mkdir data path: FF ...
    data path: /storage/data1/data, mkdir sub dir done.
```





查看fdfs_storaged服务是否已经正常启动




    
```sh
service fdfs_storaged status
```





关闭 fdfs_trackerd服务。  

因为FastDFS Storage Server服务器只需要启动 fdfs_storaged服务。




    
```sh 
chkconfig fdfs_trackerd off
chkconfig --list|grep fdfs
```





输出




    
``` 
    fdfs_storaged   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
    fdfs_trackerd   0:关闭  1:关闭  2:关闭  3:关闭  4:关闭  5:关闭  6:关闭
```





到此为止，192.168.122.115服务器上的FastDFS Storage Server 已经安装完成并启动。





之后，需要在第2个 FastDFS Storage Server 节点(`192.168.122.116`)上执行相同的步骤，安装并启动 fdfs_storaged进程。






停止进程，可以使用命令：




    
```sh 
service fdfs_storaged stop
```





# 附录





storage.conf配置文件说明





    配置说明转自51CTO，地址无法确认，不好意思




    
```
    disabled=false
    #配置是否生效
    group_name=group1
    #storage所在组(卷)
    bind_addr=192.168.6.100
    # 绑定IP，另一太 storage IP为 192.168.6.101
    client_bind=true
    #bind_addr通常是针对server的。当指定bind_addr时，本参数才有效。
    port=23000
    # 是storage 服务端口
    connect_timeout=30
    # 连接超时时间，针对socket套接字函数connect
    network_timeout=60
    # storage server 网络超时时间，单位为秒。
    heart_beat_interval=30
    # 心跳间隔时间，单位为秒
    stat_report_interval=60
    # storage server向tracker server报告磁盘剩余空间的时间间隔，单位为秒。
    base_path=/home/eric
    # base_path 目录地址,根目录必须存在  子目录会自动生成
    # 会产生data（数据存储地方）、 logs日志文件
    max_connections=256
    # 最大连接数
    buff_size = 256KB
    # 设置队列结点的buffer大小。
    work_threads=4
    # 工作线程数
    disk_rw_separated = true
    # 磁盘IO读写是否分离，缺省是分离的。
    disk_reader_threads = 1
    # 针对单个存储路径的读线程数，缺省值为1
    disk_writer_threads = 1
    # 针对单个存储路径的写线程数，缺省值为1
    sync_wait_msec=200
    # 同步文件时，如果从binlog中没有读到要同步的文件，休眠N毫秒后重新读取，0表示不休眠，立即再次尝试读取。
    sync_interval=0
    #  同步上一个文件后，再同步下一个文件的时间间隔，单位为毫秒，0表示不休眠，直接同步下一个文件。
    sync_start_time=00:00
    sync_end_time=23:59
    # 允许系统同步的时间段 (默认是全天) 。一般用于避免高峰同步产生一些问题而设定，相信sa都会明白。
    write_mark_file_freq=500
    # 把storage的mark文件定期同步到磁盘的时间间隔，单位为秒
    store_path_count=1
    # 存放文件时storage server支持多个路径（例如磁盘）。这里配置存放文件的基路径数目，通常只配一个目录。
    store_path0=/home/eric
    #  逐一配置store_path个路径，索引号基于0。注意配置方法后面有0,1,2 ......，需要配置0到store_path - 1。
    # 如果不配置base_path0，那边它就和base_path对应的路径一样。
    subdir_count_per_path=32
    # FastDFS存储文件时，采用了两级目录。这里配置存放文件的目录个数
    tracker_server=192.168.6.188:22122
    # tracker_server 的列表 要写端口的哦
    log_level=info
    # 日志级别
    run_by_group=
    # 运行storage 用户组
    run_by_user=
    # 运行storage 用户
    allow_hosts=*
    # 允许连接IP列表
    file_distribute_path_mode=0
    # 文件在data目录下分散存储策略。
    # 0: 轮流存放
    # 1: 随机存储
    file_distribute_rotate_count=100
    # 当上面的参数file_distribute_path_mode配置为0（轮流存放方式）时，本参数有效。
    #当一个目录下的文件存放的文件数达到本参数值时，后续上传的文件存储到下一个目录中
    fsync_after_written_bytes=0
    # 当写入大文件时，每写入N个字节，调用一次系统函数fsync将内容强行同步到硬盘。0表示从不调用fsync
    sync_log_buff_interval=10
    # 同步或刷新日志信息到硬盘的时间间隔，单位为秒
    sync_binlog_buff_interval=60
    # 同步binglog（更新操作日志）到硬盘的时间间隔，单位为秒
    sync_stat_file_interval=300
    #　把storage的stat文件同步到磁盘的时间间隔，单位为秒。
    thread_stack_size=512KB
    ＃　线程栈的大小。FastDFS server端采用了线程方式。
    ＃　线程栈越大，一个线程占用的系统资源就越多。
    upload_priority=10
    本storage server作为源服务器，上传文件的优先级，可以为负数。值越小，优先级越高。这里就和 tracker.conf 中store_server= 2时的配置相对应了
    if_alias_prefix=
    check_file_duplicate=0
    ＃　是否检测上传文件已经存在。如果已经存在，则不存在文件内容，建立一个符号链接以节省磁盘空间。　结合　fastdfh使用的。 1是检测，0是不检测，我们不使用fastdfh 当然 0　
    key_namespace=FastDFS
    # 当上个参数设定为1 或 yes时 (true/on也是可以的) ， 在FastDHT中的命名空间
    keep_alive=0
    # 与FastDHT servers 的连接方式 (是否为持久连接)
     
    # 下面是http的配置了就不多说了
    http.disabled=true
    http.domain_name=
    http.server_port=80
    http.trunk_size=256KB
    http.need_find_content_type=true
```





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



