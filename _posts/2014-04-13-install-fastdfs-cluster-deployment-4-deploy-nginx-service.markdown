---
author: chris.hill
comments: true
date: 2014-04-13 
excerpt: '安装nginx作为web前端，通过HTTP访问后端FastDFS存储。本文chris.hill将通过实例为大家介绍如何在Tracker Server和Storage
  Server服务器上部署Nginx服务。 '
layout: post
slug: install-fastdfs-cluster-deployment-4-deploy-nginx-service
title: FastDFS集群安装部署[4]:部署Nginx服务
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


Storage Server的安装步骤请参考博文

[FastDFS集群安装部署[2]:安装Storage Server](http://www.weittor.com/fastdfs-cluster-deployment-2-installing-storage-server.html)





有关的tracker server的端口号信息如下





    192.168.122.113:22122






接下来，_chris.hill_将为大家介绍访问FastDFS Web服务的Nginx搭建过程。





结果：安装nginx作为web前端，通过HTTP访问后端FastDFS存储。  

在storage上安装的nginx主要为了提供http的访问服务，同时解决group中storage服务器的同步延迟问题。





# 在FastDFS Storage上安装nginx





以下操作在`FastDFS storage server01    192.168.122.115`服务器上执行




<!-- more -->


## 安装依赖软件




```sh
yum -y install pcre-devel openssl openssl-devel
```





## 下载nginx和fastdfs-nginx-module




    
```sh
cd install/
wget http://nginx.org/download/nginx-1.5.9.tar.gz
wget https://fastdfs-nginx-module.googlecode.com/files/fastdfs-nginx-module_v1.15.tar.gz
```





将这两个软件scp到另外一个storage server节点以及track server节点。




    
```sh
scp nginx-1.5.9.tar.gz fastdfs-nginx-module_v1.15.tar.gz 192.168.122.116:~/install
scp nginx-1.5.9.tar.gz fastdfs-nginx-module_v1.15.tar.gz 192.168.122.113:~/install
```





## 安装nginx和fastdfs-nginx-module





解压




    
```sh
tar xvzf nginx-1.5.9.tar.gz
tar xvzf fastdfs-nginx-module_v1.15.tar.gz
```





### 编译并安装nginx




    
```sh
cd nginx-1.5.9
./configure --prefix=/usr/local/nginx --add-module=/root/install/fastdfs-nginx-module/src
make
make install
```





将FastDFS的nginx插件模块的配置文件copy到FastDFS配置文件目录。




    
```sh
cp ~/install/fastdfs-nginx-module/src/mod_fastdfs.conf /etc/fdfs
```





### 编辑nginx.conf配置文件




    
```sh
vim /usr/local/nginx/conf/nginx.conf
```





设置添加storage server的配置并保存




    
```nginx
    ......
    server {
            listen       9090;
            server_name  localhost;
    ......
            location ~/group1/M00 {
                    root /storage/data1/data;
                    ngx_fastdfs_module;
    ...... 
    } 
    ......
```





创建store_path的软连接




    
```sh
ln -s /storage/data1/data /storage/data1/data/M00
```





编辑/etc/fdfs下的配置文件mod_fastdfs.conf，添加storage信息。




    
```sh
vim /etc/fdfs/mod_fastdfs.conf
```





修改相应的配置信息




    
```
    ......
     
    # the base path to store log files
    base_path=/storage/data1
    ...
    # FastDFS tracker_server can ocur more than once, and tracker_server format is
    #  "host:port", host can be hostname or ip address
    # valid only when load_fdfs_parameters_from_tracker is true
    tracker_server=192.168.122.113:22122
     
    # the port of the local storage server
    # the default value is 23000
    storage_server_port=23000
    # the group name of the local storage server
    group_name=group1
    # if the url / uri including the group name
    # set to false when uri like /M00/00/00/xxx
    # set to true when uri like ${group_name}/M00/00/00/xxx, such as group1/M00/xxx
    # default value is false
    url_have_group_name = true
     
    # path(disk or mount point) count, default value is 1
    # must same as storage.conf
    store_path_count=1
     
    # store_path#, based 0, if store_path0 not exists, it's value is base_path
    # the paths must be exist
    # must same as storage.conf
    store_path0=/storage/data1
    
    # set the group count
    # set to none zero to support multi-group
    # set to 0  for single group only
    # groups settings section as [group1], [group2], ..., [groupN]
    # default value is 0
    # since v1.14
    group_count = 1
     
    # group settings for group #1
    # since v1.14
    [group1]
    group_name=group1
    storage_server_port=23000
    store_path_count=1
    store_path0=/storage/data1
    
    
    ......
```





### 测试并启动nginx服务




    
```sh
/usr/local/nginx/sbin/nginx -t
/usr/local/nginx/sbin/nginx
ps -ef|grep nginx
```





查看nginx日志，看是否有错误




    
```sh
tail /usr/local/nginx/logs/error.log
```





在error.log中没有错误，既启动成功。可以打开浏览器，直接访问http://192.168.122.115:9090，查看是否弹出nginx欢迎页面。





### 设置开机自启动




    
```sh
echo "/usr/local/nginx/sbin/nginx" >> /etc/rc.local
```





## 配置另一个storage server





在192.168.122.116服务器上，也执行同样的操作。
安装并启动nginx服务。
在此忽略配置过程。





# 配置Track Server的nginx服务





在Tracker Server上安装的nginx主要是为了实现反向代理和负载均衡功能。





## 安装相关依赖




    
```sh
yum -y install pcre-devel openssl openssl-devel
```





## 安装nginx




    
```sh
cd install/
tar xvzf nginx-1.5.9.tar.gz
cd nginx-1.5.9
./configure --prefix=/usr/local/nginx
make
make install
```





## 配置nginx




    
```sh
vim /usr/local/nginx/conf/nginx.conf
```





修改相应的配置信息如下




    
```nginx
    ...
    
            upstream fdfs_group1 {
                    server 192.168.122.115:9090 weight=1 max_fails=2 fail_timeout=30s;
                    server 192.168.122.116:9090 weight=1 max_fails=2 fail_timeout=30s;
     
                    }
    
        server {
            listen       8080;
            server_name  localhost;
            location ~ /group1/M00 {
                            proxy_pass http://fdfs_group1;
            }
    
    ....
```





## 启动nginx服务




    
```sh
/usr/local/nginx/sbin/nginx -t
/usr/local/nginx/sbin/nginx
```





确认nginx服务启动正常




    
```sh
ps -ef|grep nginx
tail -f /usr/local/nginx/logs/error.log
```





## 设置开机自启动




    
```sh
echo "/usr/local/nginx/sbin/nginx" >> /etc/rc.local
```





若日志里没有出现错误信息，之后可以进入下一步进行测试。





# 测试





在192.168.122.120客户端服务器上进行测试。
尝试上传一个文件到FastDFS，然后访问该文件。
配置client.conf文件。




    
```sh
vim /etc/fdfs/client.conf
```





修改参数：




    
```
    base_path=/var/fdfs                #日志存放路径
    tracker_server=192.168.122.113:22122         #tracker server的IP地址和端口号
    http.tracker_server_port=8080              #tracker server的http端口号
```





使用/usr/local/bin/fdfs_upload_file上传一个文件，程序会自动返回文件的URL。




    
```sh
fdfs_upload_file /etc/fdfs/client.conf weittor.com.1.html
```





输出信息




    
```sh
fdfs_upload_file /etc/fdfs/client.conf weittor.com.1.html
```





输出信息




    
```
    group1/M00/00/00/wKh6dFNCM8eALk-aAAAAM0bdRic.1.html
```





通过浏览器访问该url，测试是否正常




    
    
<http://192.168.122.113:8080/group1/M00/00/00/wKh6dFNCM8eALk-aAAAAM0bdRic.1.html>
    





正常情况下返回该文件的内容，如下





![](http://www.weittor.com/wp-content/uploads/2014/04/2014-4-14_FastDFS-001_800x396.png)





至此，配置过程结束。





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



