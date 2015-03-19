---
author: chris.hill
comments: true
date: 2012-07-10
excerpt: 介绍强大的命令行文件传输工具Curl使用方法。
layout: post
slug: the-example-about-curl
title: Curl工具使用实例
categories:
- IT运维
tags:
- Linux
---

* * *





与非交互式的下载工具wget相比，curl是另外一个功能强大的能够与服务器进行上传下载数据交互的数据传输工具。  

它支持的协议有：HTTP,HTTPS,FTP,FTPS,SCP,SFTP,TFTP,DICT,TELNET,LDAP以及FILE。





Curl作为在命令行方式下工作的文件传输工具，具有许多优良的特性：



  * 支持代理   

  * 用户认证   

  * FTP上传   

  * HTPP传输   

  * SSL加密链接   

  * Cookies   

  * 文件断点续传   


<!-- more -->



# Curl下载文件





## 允许从远程http/ftp服务器下载文件




    
```sh
curl -o output.file http://server1.cyberciti.biz/file.tar.gz
```





或者




    
    
```sh
curl -O http://server1.cyberciti.biz/file.tar.gz
```





或者




    
    
```sh
curl --remote-name http://server1.cyberciti.biz/file.tar.gz
```





## 将网页文件下载到本地




    
    
```sh
curl -o nixcraft.html http://www.cyberciti.biz/low.html
```





## 同时下载多个文件




    
    
```sh
curl -O http://www.cyberciti.biz/low.html -O http://bash.cyberciti.biz/dl/581.sh.zip
```





# Curl从ssh服务器下载文件





## 使用SFTP协议从SSH服务器上安全地获取文件




    
    
```sh
curl -u username sftp://server1.cyberciti.biz/path/to/file.txt
```





或者 (注意：~ 代表你的用户主目录 $HOME)




    
    
```sh
curl -u vivek sftp://home1.cyberciti.biz/~/docs/resume.pdf
```





## 使用SCP协议下载文件





还可以通过使用SCP协议，以公钥认证的方式从SSH服务器获取文件。




    
    
```sh
curl -u username: --key ~/.ssh/id_rsa --pubkey ~/.ssh/id_rsa.pub scp://home1.cyberciti.biz/~/Videos/rhn_register.ogv
```





这里





```
    -u username - 指定用户名  

    -u username:password - 指定用户名和密码  

    --key ~/.ssh/id_rsa - SSL或者SSH的私钥文件名称  

    --pubkey ~/.ssh/id_rsa.pub - SSH的公钥文件名称  

    scp://home1.cyberciti.biz/~/Videos/rhn_register.ogv - 使用scp协议下载指定的文件
```





# Curl:通过用户名和密码下载文件





## 使用ftp协议下载文件




    
    
```sh
curl ftp://username:passwd@ftp1.cyberciti.biz:21/path/to/backup.tar.gz
```




或者




    
    
```sh
curl -u UserName:PassWord ftp://ftp1.cyberciti.biz:21/backups/07/07/2012/mysql.blog.sql.tar.gz
```





## 加密的ftp协议




    
    
```sh
curl --ftp-ssl -u UserName:PassWord ftp://ftp1.cyberciti.biz:21/backups/07/07/2012/mysql.blog.sql.tar.gz
```





## 通过HTTP下载





### HTTP下载





使用如下命令




    
    
```sh
curl http://username:passwd@server1.cyberciti.biz/file/path/data.tar.gz
```





或者




    
    
```sh
curl -u Username:Password http://server1.cyberciti.biz/file/path/data.tar.gz
```





## 更多内容





请查看




    
    
```sh
man curl
```





# 参考资料





网址：[http://goo.gl/wcZZ6](http://goo.gl/wcZZ6)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



