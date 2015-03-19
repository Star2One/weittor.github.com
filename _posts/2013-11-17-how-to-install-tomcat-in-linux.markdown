---
author: chris.hill
comments: true
date: 2013-11-17 
excerpt: 本文chris.hill为大家介绍在Linux系统上安装tomcat服务的方法。后续会继续带来有关tomcat服务的其他内容。欢迎关注。
layout: post
slug: how-to-install-tomcat-in-linux
title: apache-tomcat安装
categories:
- Web应用
tags:
- Tomcat
---

* * *





    版本信息
        apache tomcat 7
        jdk 1.7




# 前期准备





## 配置JDK







  * 由于64位版本会提供更好的性能，故建议部署64位版本JDK在64位系统上





这里选择jdk1.7_x64位版本



<!-- more -->



官方下载地址：[Oracle链接](http://www.oracle.com/technetwork/java/javase/downloads/index.html)







  * _chris hill_将常用的JDK共享到了百度网盘中，大家可以按需求下载，速度比官方下载快许多





[_chris hill_共享地址](http://pan.baidu.com/s/1tcKJS)





## 下载tomcat





这里选择zip格式的JDK安装包_jdk1.7.0_25-linux-x64.zip_





## 解压软件包



```sh
unzip -d /opt jdk1.7.0_25-linux-x64.zip
```




这里安装jdk到/opt目录下




    
```sh
ls /opt 
jdk1.7.0_25-linux-x64
```



查看软件包信息

    
```sh
ls /opt/jdk1.7.0_25-linux-x64/java-linux-x64/
bin db jre LICENSE README.html THIRDPARTYLICENSEREADME-JAVAFX.txt
COPYRIGHT include lib man release THIRDPARTYLICENSEREADME.txt
```





目录说明




```
    bin  ---  执行脚本存放目录
    jre  ---  调用java函数所需要的函数库
    lib  ---  JDK工具用到的类库及其他文件
    man  ---  java有关的帮助文件
    demo  --- 含有源代码的程序示例
    include  ---  C 语言头文件
    db  ---  纯Java开发的数据库 Apache Derby
```


## 配置JAVA系统变量





设定JAVA_HOME和PATH系统变量
编辑.bash_profile文件




    
```sh
vi .bash_profile
```


```
JAVA_HOME=/opt/jdk1.7.0_25-linux-x64/java-linux-x64 
PATH=$JAVA_HOME/bin:$PATH:$HOME/bin 
export PATH JAVA_HOME
```




    
```sh
source .bash_profile
```




执行命令




    
```sh
java -version
```




若输出java的相关信息，说明安装成功




```
java version "1.7.0_25"
Java(TM) SE Runtime Environment (build 1.7.0_25-b15)
Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)
``` 





# 安装配置





## 下载tomcat安装包





下载地址[Apache tomcat](http://tomcat.apache.org/download-70.cgi)





这里_chris hill_选择下载zip的软件包apache-tomcat-7.0.42.zip





## 解压该软件包




    
```sh
unzip -d /opt apache-tomcat-7.0.42.zip
```




## 启动tomcat





需要赋予tomcat执行文件执行权限




    
```sh
cd /opt/apache-tomcat-7.0.42/bin</br>
chmod 0755 *.sh
```





查看tomcat版本信息




    
```sh
./version.sh
```




输出




```
  Using CATALINA_BASE: /opt/apache-tomcat-7.0.42
  Using CATALINA_HOME: /opt/apache-tomcat-7.0.42
  Using CATALINA_TMPDIR: /opt/apache-tomcat-7.0.42/temp
  Using JRE_HOME: /opt/jdk1.7.0_25-linux-x64/java-linux-x64
  Using CLASSPATH: /opt/apache-tomcat-7.0.42/bin/bootstrap.jar:/opt/apache-tomcat-7.0.42/bin/tomcat-juli.jar
  Server version: Apache Tomcat/7.0.42
  Server built: Jul 2 2013 08:57:41
  Server number: 7.0.42.0
  OS Name: Linux
  OS Version: 2.6.32-220.el6.x86_64
  Architecture: amd64
  JVM Version: 1.7.0_25-b15
  JVM Vendor: Oracle Corporation
```






测试配置文件正确性




    
```sh
./configtest.sh
```




输出




```
  Using CATALINA_BASE: /opt/apache-tomcat-7.0.42
  Using CATALINA_HOME: /opt/apache-tomcat-7.0.42
  Using CATALINA_TMPDIR: /opt/apache-tomcat-7.0.42/temp
  Using JRE_HOME: /opt/jdk1.7.0_25-linux-x64/java-linux-x64
  Using CLASSPATH: /opt/apache-tomcat-7.0.42/bin/bootstrap.jar:/opt/apache-tomcat-7.0.42/bin/tomcat-juli.jar
  十一月 17, 2013 9:19:02 下午 org.apache.catalina.core.AprLifecycleListener init
  INFO: The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path:
  /usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
  十一月 17, 2013 9:19:02 下午 org.apache.catalina.startup.SetAllPropertiesRule begin
  WARNING: [SetAllPropertiesRule]{Server/Service/Connector} Setting property 'maxthreads' to '250' did not find a matching property.
  十一月 17, 2013 9:19:03 下午 org.apache.coyote.AbstractProtocol init
  INFO: Initializing ProtocolHandler ["http-bio-8080"]
  十一月 17, 2013 9:19:03 下午 org.apache.coyote.AbstractProtocol init
  INFO: Initializing ProtocolHandler ["ajp-bio-8009"]
  十一月 17, 2013 9:19:03 下午 org.apache.catalina.startup.Catalina load
  INFO: Initialization processed in 2050 ms
```







确认配置无误之后，启动tomcat服务




    
```sh
./startup.sh
```




默认通过浏览器访问(http://localhost:8080)





即可看到tomcat首页的相关信息





## 关闭tomcat服务




    
```sh
./shutdown.sh
```




提示：为了便于管理，可以根据不同服务编写相应tomcat服务的shell脚本





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://wwww.weibo.com/weittor)



