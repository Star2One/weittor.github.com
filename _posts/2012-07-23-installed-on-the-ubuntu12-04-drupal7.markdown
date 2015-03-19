---
author: chris.hill
comments: true
date: 2012-07-23
excerpt: Drupal是一个开源的内容管理系统(CMS) 平台，主要用于构造提供多种功能和服务的动态网站。本文介绍如何在ubuntu12.04上安装Drupal7...
layout: post
slug: installed-on-the-ubuntu12-04-drupal7
title: 在Ubuntu12.04上安装drupal7
categories:
- IT运维
tags:
- Linux
---

* * *

#简介  


Drupal是一个开源的内容管理系统(CMS) 平台，它是用PHP写成的。  
主要用于构造提供多种功能和服务的动态网站，这些功能包括用户管理(UserAdministration)、发布工作流 (Publishing Workflow)、讨论、新闻聚合(NewsAggregation)、元数据(Metadata)操作和用于内容共享的XML发布。Drupal有一个优秀的模块化结构，提供了许多模块，包括短消息、个性化书签、网站管理、Blog、日记、电子商务、电子出版、留言簿、Job、网上电影院、论坛、投票 等模块，而且Drupal模块的下载、安装、定制非常方便。

#准备工作

##安装LAMP组件

执行命令：
    
```sh
sudo tasksel
```

选择LAMP组件，安装完成即可  

<!-- more -->

##下载drupal7 

最新版本为7.14   

地址：

[http://ftp.drupal.org/files/projects/drupal-7.14.tar.gz](http://ftp.drupal.org/files/projects/drupal-7.14.tar.gz)

解压缩  

    
```sh
tar xvzf drupal-7.14.tar.gz
```
移动解压目录到系统的www网站目录  

    
```sh
mv drupal-7.14 /var/www/drupal7
```
修改相应的权限
    
```sh
cd /var/www/drupal7/
chown -R www-data.www-data /var/www/drupal7
```
##创建数据库

    数据库名称：drupal7   
    用户名：drupal7   
    密码：54321  
  
执行如下命令：
    
```sh
mysqladmin -u root -p create drupal7
```
登录到mysql的客户端
    
```sh
mysql -u root -p
```
添加drupal7用户，  
执行命令：  

    
```mysql
mysql>GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON drupal7.* TO 'drupal7'@'localhost' IDENTIFIED BY '54321'; 
mysql> quit
```
##修改drupal7部分文件夹读写权限 
    
```sh
cd /var/www/drupal7
chmod a+w sites/default
```
提示：安装完成之后，请修改该文件的权限，执行命令： 
    
```sh
chmod go-w sites/default
```
##创建默认配置文件并修改相应的权限 
    
```sh
cp sites/default/default.settings.php sites/default/settings.php
chown www-data.www-data sites/default/settings.php
chmod a+w sites/default/settings.php
```
提示：安装完成之后，请修改该文件的权限，  
执行命令： 
    
```sh
chmod go-w sites/default/settings.php
```
## 执行安装

在浏览器中打开安装界面进行安装 

[http://192.168.1.103/drupal7/install.php](http://192.168.1.103/drupal7/install.php)

选择标准安装  
  
![](/images/2012year/2012-07-21_drupal7-01.png)   

安装语言English  
  
![](/images/2012year/2012-07-21_drupal7-02.png)  
  
  
填写数据库相关信息  
  
![](/images/2012year/2012-07-21_drupal7-03.png)  

初始化  
  
![](/images/2012year/2012-07-21_drupal7-04.png)  

填写自己的网站信息  
  
![](/images/2012year/2012-07-21_drupal7-05.png)
![](/images/2012year/2012-07-21_drupal7-06.png)  

安装完成  
  
![](/images/2012year/2012-07-21_drupal7-07.png)  
  
访问自己的网站

[http://192.168.1.103/drupal7](http://192.168.1.103/drupal7)  
  

* * *

作者：chris hill   

微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor) 

