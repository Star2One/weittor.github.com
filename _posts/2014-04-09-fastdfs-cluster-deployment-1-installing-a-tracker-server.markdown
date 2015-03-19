---
author: chris.hill
comments: true
date: 2014-04-09
excerpt: 'FastDFS是一个开源的轻量级分布式文件系统，功能包括：文件存储、文件同步、文件访问（文件上传、文件下载）等，解决了大容量存储和负载均衡的问题。特别适合应用于以文件为载体的在线服务，如相册网站、视频网站等。

  FastDFS服务端有两个角色：跟踪器（tracker server）和存储节点（storage server）。跟踪器主要起调度工作，在访问上起负载均衡的作用。存储节点用于存储数据，直接向前端服务提供数据。

  接下来，chris.hill将通过实例为大家介绍有关FastDFS集群的搭建过程。'
layout: post
slug: fastdfs-cluster-deployment-1-installing-a-tracker-server
title: FastDFS集群安装部署[1]:安装Tracker Server
categories:
- 网络存储应用
tags:
- FastDFS
---

* * *





# FastDFS+Nginx图片存储方案





FastDFS是一个开源的轻量级分布式文件系统，功能包括：文件存储、文件同步、文件访问（文件上传、文件下载）等，解决了大容量存储和负载均衡的问题。特别适合应用于以文件为载体的在线服务，如相册网站、视频网站等。  

FastDFS服务端有两个角色：跟踪器（tracker server）和存储节点（storage server）。  

跟踪器主要起调度工作，在访问上起负载均衡的作用。存储节点用于存储数据，直接向前端服务提供数据。





接下来，_chris.hill_将通过实例为大家介绍有关FastDFS集群的搭建过程。





**服务器IP规划**





> FastDFS storage server01    192.168.122.115  
> FastDFS storage server02    192.168.122.116  
> FastDFS tracker server01    192.168.122.113  
> FastDFS client    192.168.122.120





**架构图如下**





![](/images/2014year/2014040701.FastDFS.Cluster.Arch.png)





# 部属FastDFS Tracker Server





以下操作在`FastDFS tracker server01    192.168.122.113`服务器上进行




<!-- more -->


## 安装依赖软件包


```sh 
yum install -y wget gcc make
```    





## 安装libevent网络库





FastDFS依赖开源网络库libevent，因此先安装它。  

libevent的官方站点：[http://libevent.org/](http://libevent.org/)  

下载并安装最新版。




    
```sh
mkdir install   #创建目录
cd install
wget "https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz" ##下载
tar zxf libevent-2.0.21-stable.tar.gz   #解压
cd libevent-2.0.21-stable
./configure --prefix=/usr  #编译
make
make install    #安装
ldconfig    #使相关库依赖生效
``` 





## 安装FastDFS Tracker Server





下载地址：(http://code.google.com/p/fastdfs/)




    
```sh
cd ～/install/
wget http://fastdfs.googlecode.com/files/FastDFS_v4.06.tar.gz   #下载
tar xvzf FastDFS_v4.06.tar.gz   #解压
cd FastDFS
ls
```





编辑安装配置文件make.sh




    
```sh
vim make.sh
###取消WITH_LINUX_SERVICE=1选项前面的注释
```





> _提示_:新版的FastDFS去掉了内置的HTTP服务，因此无需修改有关HTTP服务的配置  

执行安装
    
```sh
./make.sh
./make.sh install
```





查看fdfs进程启动选项




    
```sh
chkconfig --list | grep fdfs
```





输出

``` 
    fdfs_storaged   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
    fdfs_trackerd   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
``` 





fastdfs默认的配置文件被放置在/etc/fdfs目录下面，包含了
_client.conf  http.conf  mime.types  storage.conf  tracker.conf_这五个文件。  

fastdfs进程的启动是以加载的配置文件区分的。





## 配置FastDFS Tracker Server





FastDFS的配置文件在/etc/fdfs目录下




    
```sh
ls /etc/fdfs
client.conf  http.conf  mime.types  storage.conf  tracker.conf
```





配置文件定义




    
```
    client.conf    客户端上传配置文件
    storage.conf    文件存储服务器配置文件
    tracker.conf    负责均衡调度服务器配置文件
    http.conf        http服务器配置文件
``` 





修改 tracker.conf
配置FastDFS Tracker Server，需要修改 tracker.conf 配置文件




    
```sh
vim /etc/fdfs/tracker.conf
...
# the base path to store data and log files
# 数据和日志的存储目录，需要自行创建该目录
base_path=/var/fdfs
...
     
# HTTP port on this tracker server
#HTTP服务的端口号
http.server_port=9090
...
```





提示：其他配置选项保持默认。新版的FastDFS去掉的内置的HTTP server，因此后续需要集成nginx作为默认的http server。





创建数据存储目录




    
```sh
mkdir -p /var/fdfs
``` 





启动fdfs_trackerd服务




    
```sh
service fdfs_trackerd start
Starting FastDFS tracker server:
```





注意：执行脚本既不会显示成功也不会显示失败，所以需要确认一下状态。





确认fdfs_trackerd是否已经正常启动




    
```sh
service fdfs_trackerd status
```





查看fdfs_trackerd的日志




    
```sh
tail -f /var/fastdfs/logs/trackerd.log
```





关闭fdfs_storaged 服务。因为这台机器上只需要启动 fdfs_trackerd 服务。




    
```sh
chkconfig fdfs_storaged off
chkconfig --list | grep fdfs
```





输出




    
```
    fdfs_storaged   0:关闭  1:关闭  2:关闭  3:关闭  4:关闭  5:关闭  6:关闭
    fdfs_trackerd   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
```





最后，查看一下FastDFS存储目录里面的内容




    
```sh
ls /var/fastdfs/
data  logs
tree /var/fastdfs/
```





到此为止，192.168.122.113服务器上的FastDFS Tracker Server 已经安装完成并启动。





停止进程，可以使用命令：




    
```sh
service fdfs_trackerd stop
```





下一步我们将部署FastDFS Storage Server ，请参考后续博文。





# 附录





> 配置说明转载于51CTO博客，具体哪位童鞋写的，找不到了，不好意思。






tracker.conf配置文件说明




    
```
    disabled=false
    #配置是否生效
    bind_addr=192.168.6.102
    #绑定IP
    port=22122
    #服务端口
    connect_timeout=30
    #连接超时时间
    network_timeout=60
    #  tracker server的网络超时，单位为秒。
    base_path=/home/yangzi
    #目录地址，里面会创建data(存放存储服务器信息)、logs，日志文件
    max_connections=256
    #系统提供服务最大连接数
    work_threads=4
    #线程数，通常设置CPU数
    store_lookup=2
    #上传组(卷) 的方式 0:轮询方式 1: 指定组 2: 平衡负载(选择最大剩余空间的组(卷)上传)
    #这里如果在应用层指定了上传到一个固定组,那么这个参数被绕过
    store_group=group1
    #当上一个参数设定为1 时 (store_lookup=1，即指定组名时)，必须设置本参数为系统中存在的一个组名。如果选择其他的上传方式，这个参数就没有效了
    store_server=0
    #选择哪个storage server 进行上传操作(一个文件被上传后，这个storage server就相当于这个文件的storage server源，会对同组的storage server推送这个文件达到同步效果)
    # 0: 轮询方式
    # 1: 根据ip 地址进行排序选择第一个服务器（IP地址最小者）
    # 2: 根据优先级进行排序（上传优先级由storage server来设置，参数名为upload_priority）
    store_path=0
    #选择storage server 中的哪个目录进行上传。storage server可以有多个存放文件的base path（可以理解为多个磁盘）。
    # 0: 轮流方式，多个目录依次存放文件
    # 2: 选择剩余空间最大的目录存放文件（注意：剩余磁盘空间是动态的，因此存储到的目录或磁盘可能也是变化的）
    download_server=0
    #选择哪个 storage server 作为下载服务器
    # 0: 轮询方式，可以下载当前文件的任一storage server
    # 1: 哪个为源storage server 就用哪一个 (前面说过了这个storage server源 是怎样产生的) 就是之前上传到哪个storage server服务器就是哪个了
    reserved_storage_space = 4GB
    #storage server 上保留的空间,保证系统或其他应用需求空间(指出 如果同组的服务器的硬盘大小一样,以最小的为准,也就是只要同组中有一台服务器达到这个标准了,这个标准就生效,原因就是因为他们进行备份)
    log_level=info
    #选择日志级别
    run_by_group=
    #操作系统运行FastDFS的用户组
    run_by_user=
    #操作系统运行FastDFS的用户
    allow_hosts=*
    #可以连接到此 tracker server 的ip范围（对所有类型的连接都有影响，包括客户端，storage server）
    sync_log_buff_interval = 10
    # 同步或刷新日志信息到硬盘的时间间隔，单位为秒
    # 注意：tracker server 的日志不是时时写硬盘的，而是先写内存。
    check_active_interval = 120
    # 检测 storage server 存活的时间隔，单位为秒。
    # storage server定期向tracker server 发心跳，如果tracker server在一个check_active_interval内还没有收到storage server的一次心跳，那边将认为该storage server已经下线。所以本参数值必须大于storage server配置的心跳时间间隔。通常配置为storage server心跳时间间隔的2倍或3倍。
    thread_stack_size = 64KB
    # 线程栈的大小。FastDFS server端采用了线程方式。更正一下，tracker server线程栈不应小于64KB，不是512KB。
    # 线程栈越大，一个线程占用的系统资源就越多。如果要启动更多的线程（V1.x对应的参数为max_connections，
    V2.0为work_threads），可以适当降低本参数值。
    storage_ip_changed_auto_adjust = true
    # 这个参数控制当storage server IP地址改变时，集群是否自动调整。注：只有在storage server进程重启时才完成自动调整。
    storage_sync_file_max_delay = 86400
    # V2.0引入的参数。存储服务器之间同步文件的最大延迟时间，缺省为1天。根据实际情况进行调整
    storage_sync_file_max_time = 300
    # V2.0引入的参数。存储服务器同步一个文件需要消耗的最大时间，缺省为300s，即5分钟。
    http.disabled=true
    # HTTP服务是否不生效 当然编译的时候我已经把 with_httpd宏去掉了，
    http.server_port=80
    # HTTP服务端口
    # 下列参数只有 开启http服务才有用
    http.check_alive_interval=30
    http.check_alive_type=tcp
    http.check_alive_uri=/status.html
    http.need_find_content_type=true
```





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



