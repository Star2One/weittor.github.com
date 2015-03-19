---
author: chris.hill
comments: true
date: 2013-10-22
excerpt: 默认，当对tomcat服务器进行访问时，如果访问页面不存在的话，tomcat在返回报错信息的同时，会在错误页面底部显示tomcat的版本号信息。在企业应用环境中，泄露应用服务的版本信息存在着极大的安全风险，尤其是当你所使用的tomcat
  服务没有及时进行更新的情况下。如何隐藏tomcat版本信息呢？Let's go.
layout: post
slug: how-to-hide-a-tomcat-version-information
title: 如何隐藏tomcat版本信息
categories:
- Web应用
tags:
- Tomcat
---

* * *





# 简介





Apache Tomcat作为Java Servlet和JSP解析器，在企业有着广泛地应用。





默认，当对tomcat服务器进行访问时，如果访问页面不存在的话，tomcat在返回报错信息的同时，会在错误页面底部显示tomcat的版本号信息。





如下所示





![tomcat报错信息1](/images/2013year/2013-10-22_how-to-hide-tomcat-version01.png)





在企业应用环境中，泄露应用服务的版本信息存在着极大的安全风险，尤其是当你所使用的tomcat 服务没有及时进行更新的情况下





如何隐藏tomcat版本信息呢？
Let's go.



<!-- more -->



# 解决方法





1.切换到$CATALINA_HOME/lib目录，创建org/apache/catalina/util目录。





_chris hill_的 $CATALINA_HOME 目录为/opt/tomcat7




    
    
```sh
cd /opt/tomcat7/lib
mkdir -p org/apache/catalina/util
```





2.进入到刚刚创建的目录内，创建配置文件ServerInfo.properties





编辑该文件，添加以下内容




    
```sh    
cd org/apache/catalina/util
vi ServerInfo.properties
```
内容如下

```
server.info=Apache Tomcat Version X
```





3.之后重启tomcat服务




    
    
```sh
cd /opt/tomcat7/bin
./catalina.sh stop
./catalina.sh start
```





接下来当你再次访问曾经的错误页面时，你将不会看到过去的tomcat版本信息，取而代之地是刚刚所编辑_server.info_参数的内容。





![tomcat报错信息2](/images/2013year/2013-10-22_how-to-hide-tomcat-version02.png)





**Tips**
命令行查看tomcat版本信息，可以执行如下命令




    
```sh
bash /opt/tomcat7/bin/version.sh
```

输出如下


```
Server version: Apache Tomcat/7.0.42
Server built:   Jul 2 2013 08:57:41
Server number:  7.0.42.0
OS Name:        Linux
OS Version:     2.6.32-220.el6.x86_64
Architecture:   amd64
JVM Version:    1.7.0_25-b15
JVM Vendor:     Oracle Corporation
..
```





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



