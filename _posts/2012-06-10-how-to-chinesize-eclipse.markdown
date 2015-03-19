---
author: chris.hill
comments: true
date: 2012-06-10
excerpt: 本文介绍如何对Eclipse软件进行汉化。
layout: post
slug: how-to-chinesize-eclipse
title: 如何对Eclipse进行汉化
categories:
- 脚本工具
tags:
- Python
---

* * *





上一篇文章曾介绍了如何在Ubuntu12.04系统上《[搭建基于Eclipse的python开发环境](http://blog.weittor.com/how-to-install-the-python-development-environment-with-eclipse.html)》，由于默认Eclipse没有包含中文语言包。  

因此，本文介绍如何对Eclipse进行汉化。





  * 操作系统：LinuxMint13(或者Ubuntu12.04)  

  * 软件：Eclipse3.7


chris.hill在此通过安装软件源的方式，对Eclipse进行汉化。



<!-- more -->


单击Help菜单，选择“Install New Software”  

之后点击“Add”按钮，添加Eclipse汉化版软件源




```
    Name: eclipse-zh  
    Location: http://download.eclipse.org/technology/babel/update-site/R0.9.1/indigo
```





之后更新软件源在软件列表中选择简体中文或者繁体中文


  * Babel Language Packs in Chinese (Simplified)     
  * Babel Language Packs in Chinese (Traditional)





![安装语言包1](/images/2012year/2012-06-08_EclipsNo.3.bmp)





确认后，进行下一步安装，期间会提示是否信任该软件，确定信任后，安装继续。  

安装完成后，重启Eclipse即可。





![安装语言包](/images/2012year/2012-06-08_EclipsNo.4.bmp)





    注：所有汉化语言包的地址
        <http://www.eclipse.org/babel/downloads.php>




* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



