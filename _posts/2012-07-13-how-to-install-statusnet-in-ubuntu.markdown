---
author: chris.hill
comments: true
date: 2012-07-13
excerpt: StatusNet是个相当老牌的开源微博程序，相当多的国外微博网站都是基于它架设的。本文介绍如何在ubuntu12.04上安装StatusNet微博程序。
layout: post
slug: how-to-install-statusnet-in-ubuntu
title: Ubuntu12.04安装StatusNet微博服务
categories:
- IT运维
tags:
- Linux
---

* * *

# 简介

StatusNet是个相当老牌的开源微博程序，相当多的国外微博网站都是基于它架设的。  
StatusNet支持文件分享、用户群组、自定义皮肤、与其它社会网络（如Twitter、facebook等）互联互通、从手机端和PC桌面发起访问、浏览器插件、短信接口、即时通信接口、多国语言包，还有很多的插件和应用。  
StatusNet兼容Twitter的API，大部分的Twitter客户端可以容易就支持StatusNet，这使得StatusNet显得非常灵活。   
StatusNet使用了包括Pear、OAuth、XMPPHP、openid、markdown、Yadis在内的大量开源程序，也使用了许多在线服务（如：snapshot、tinyurl）。   
本文介绍如何在ubuntu12.04上安装StatusNet微博程序 

# 安装LAMP服务

执行命令：


```sh
sudo tasksel
```
选择“LAMP”组件   

点击确定，期间会提示设置Mysql数据库的密码，完成安装。  
  
<!-- more -->


#安装PHP扩展组件   

执行命令：

```sh
sudo apt-get install php5-curl php5-mysql php5-gd libapache2-mod-php5 php5-cgi php5-cli
```
实际项目实施过程中，还需要安装其他组件，请参考官方说明

#安装statusnet
下载源码包

```sh
wget http://status.net/statusnet-1.1.0.tar.gz
```
解压软件包

```sh
tar xvzf statusnet-1.1.0.tar.gz
```
移动到apache2的网页主目录
    
```sh
mv statusnet-1.1.0 /var/www/statusnet
```
修改相应权限
    
```sh
sudo chown -R www-data.www-data /var/www/statusnet
```

在一些网站需要设置
    
```sh
sudo chmod a+w /var/www/statusnet
```
同时你也需要设置statusnet目录下面一些子目录的权限一种简便但不是太安全的方法，
    
```sh
chmod a+w /var/www/statusnet/avatar
chmod a+w /var/www/statusnet/background
chmod a+w /var/www/statusnet/file
```
创建数据库，用来存放微博的数据执行命令：
    
```sh
mysqladmin -u "root" --password="123456" create statusnet
```
添加新的数据库用户stutusnetuser，用来访问数据库statusnet
    
```sh
mysql -u root -p
```
进入mysql操作终端执行mysql命令
    
```sh
GRANT ALL on statusnet.* TO 'statusnetuser'@'localhost' IDENTIFIED BY '654321';
```
开始安装statusnet  

之后在浏览器中打开statusnet的安装界面

[http://localhost/statusnet/install.php](http://localhost/statusnet/install.php)   

根据提示添写数据库的链接信息以及自己设定的网站信息。   
安装程序会根据所添的信息配置安装你的微博网站。   
初始化成功后，会显示如下界面  
  
  
![](/images/2012year/2012-7-11_statusnet02.png)



#安装成功  
访问微博网站 [http://localhost/statusnet](http://localhost/statusnet)   
登录后，添加自己的相关信息进行测试。  
以下是weittor测试发送消息和事件的效果图，供参考  
  
![](/images/2012year/2012-7-11_statusnet01.png)

#参考内容

status官方安装wiki  

[](http://gitorious.org/statusnet/mainline/blobs/master/INSTALL)[http://gitorious.org/statusnet/mainline/blobs/master/INSTALL  ](http://gitorious.org/statusnet/mainline/blobs/master/INSTALL) 

statusnet下载页面  

[http://yourserver.example.com/statusnet/install.php](http://yourserver.example.com/statusnet/install.php)  
  

------

作者：chris hill   
微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)  


