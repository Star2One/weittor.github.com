---
author: chris.hill
comments: true
date: 2014-04-11
excerpt: 本文介绍FastDFS集群部署过程中，有关FastDFS 客户端安装的相关内容。
layout: post
slug: fastdfs-cluster-deployment-3-installing-fastdfs-client
title: FastDFS集群安装部署[3]:安装FastDFS Client
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

[FastDFS集群安装部署[1]:安装Tracker Server](http://blog.weittor.com/fastdfs-cluster-deployment-1-installing-a-tracker-server.html)  


Storage Server的安装步骤请参考博文

[FastDFS集群安装部署[2]:安装Storage Server](http://blog.weittor.com/fastdfs-cluster-deployment-2-installing-storage-server.html)





有关的tracker server的端口号信息如下


 
    192.168.122.113:22122






接下来，_chris.hill_将为大家介绍FastDFS Client的搭建过程。



<!-- more -->



# 部属FastDFS Client





以下操作在`FastDFS client    192.168.122.120` 服务器上进行






## 安装依赖软件包




```sh
yum install -y wget gcc make
```




## 安装libevent网络库





FastDFS依赖开源网络库libevent，因此先安装它。  

libevent的官方站点：

[http://libevent.org/](http://libevent.org/)  

下载最新版2.0.21版本。




    
```sh
mkdir install
cd install
wget "https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz"
tar zxf libevent-2.0.21-stable.tar.gz
cd libevent-2.0.21-stable
./configure --prefix=/usr
make
make install
ldconfig
```





## 安装FastDFS Client





下载地址：[http://code.google.com/p/fastdfs/](http://code.google.com/p/fastdfs/)




    
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
```





输出




    
```
    fdfs_storaged   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
    fdfs_trackerd   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
```





取消 fdfs_trackerd和fdfs_storaged开机自启动选项




    
```sh
chkconfig fdfs_storaged off
chkconfig fdfs_trackerd off
```





## 修改客户端配置




    
```sh
vim /etc/fdfs/client.conf
```





编辑配置如下




    
```
    # the base path to store log files
    #base_path=/home/yuqing/fastdfs
    base_path=/var/fastdfs
    
    # tracker_server can ocur more than once, and tracker_server format is
    #  "host:port", host can be hostname or ip address
    #tracker_server=192.168.0.197:22122
    tracker_server=192.168.122.113:22122    ##Tracker Server端口号信息
    ......
```





创建数据存储目录




    
```sh
mkdir -p /var/fastdfs
```





测试上传，FastDFS为我们提供了名为fdfs_test的测试工具




    
```sh
fdfs_test
```





输出




    
```
    This is FastDFS client test program v4.06
     
    Copyright (C) 2008, Happy Fish / YuQing
     
    FastDFS may be copied only under the terms of the GNU General
    Public License V3, which may be found in the FastDFS source kit.
    Please visit the FastDFS Home Page http://www.csource.org/
    for more detail.
     
    Usage: fdfs_test <config_file> <operation>
            operation: upload, download, getmeta, setmeta, delete and query_servers
```





先创建一个简单的文件weittor.com.txt，然后使用fdfs_test上传到FastDFS。




    
```sh
echo "Hello FastDFS" >weittor.com.txt    ##创建测试文件weittor.com.txt
fdfs_test /etc/fdfs/client.conf upload weittor.com.txt    ##上传测试文件
```





输出信息如下




    
```
    This is FastDFS client test program v4.06
     
    Copyright (C) 2008, Happy Fish / YuQing
     
    FastDFS may be copied only under the terms of the GNU General
    Public License V3, which may be found in the FastDFS source kit.
    Please visit the FastDFS Home Page http://www.csource.org/
    for more detail.
     
    [2014-04-05 20:08:24] DEBUG - base_path=/var/fdfs, connect_timeout=30, network_timeout=60, tracker_server_count=2, anti_steal_token=0, anti_steal_secret_key length=0, use_connection_pool=0, g_connection_pool_max_idle_time=3600s, use_storage_id=0, storage server id count: 0
     
    tracker_query_storage_store_list_without_group:
            server 1. group_name=, ip_addr=192.168.122.115, port=23000
            server 2. group_name=, ip_addr=192.168.122.116, port=23000
     
    group_name=group1, ip_addr=192.168.122.115, port=23000
    storage_upload_by_filename
    group_name=group1, remote_filename=M00/00/00/wKh6c1M_8jiAP8XfAAAADpNIeWE786.txt
    source ip address: 192.168.122.115
    file timestamp=2014-04-05 20:08:24
    file size=14
    file crc32=2471000417
    file url: http://192.168.122.113:8080/group1/M00/00/00/wKh6c1M_8jiAP8XfAAAADpNIeWE786.txt
    storage_upload_slave_by_filename
    group_name=group1, remote_filename=M00/00/00/wKh6c1M_8jiAP8XfAAAADpNIeWE786_big.txt
    source ip address: 192.168.122.115
    file timestamp=2014-04-05 20:08:24
    file size=14
    file crc32=2471000417
    file url: http://192.168.122.113:8080/group1/M00/00/00/wKh6c1M_8jiAP8XfAAAADpNIeWE786_big.txt
```





注意上面fdfs_test upload执行输出的信息。
将weittor.com.txt上传至 FastDFS 之后，存储信息如下：




    
```
    group_name=group1, 
    remote_filename=M00/00/00/wKh6c1M_8jiAP8XfAAAADpNIeWE786.txt
```





在 fdfs_test download 中需要用到这两个信息。





测试下载。




    
```sh
fdfs_test /etc/fdfs/client.conf download group1 M00/00/00/wK                        h6c1M_8jiAP8XfAAAADpNIeWE786.txt
```





输出信息如下：




    
```
    This is FastDFS client test program v4.06
     
    Copyright (C) 2008, Happy Fish / YuQing
     
    FastDFS may be copied only under the terms of the GNU General
    Public License V3, which may be found in the FastDFS source kit.
    Please visit the FastDFS Home Page http://www.csource.org/
    for more detail.
     
    [2014-04-05 20:11:45] DEBUG - base_path=/var/fdfs, connect_timeout=30, network_t                                                                                        imeout=60, tracker_server_count=2, anti_steal_token=0, anti_steal_secret_key len                                                                                        gth=0, use_connection_pool=0, g_connection_pool_max_idle_time=3600s, use_storage                                                                                        _id=0, storage server id count: 0
     
    storage=192.168.122.115:23000
    download file success, file size=14, file save to wKh6c1M_8jiAP8XfAAAADpNIeWE786                                                                                        .txt
```
    





查看该文件的内容




    
```sh
ls
##wKh6c1M_8jiAP8XfAAAADpNIeWE786.txt
cat wKh6c1M_8jiAP8XfAAAADpNIeWE786.txt
##Hello FastDFS
```





从上面的测试可以看出，上传和下载都成功了。





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



