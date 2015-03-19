---
author: chris.hill
comments: true
date: 2012-06-09
excerpt: 本文介绍在Ubuntu系统上如何通过PyDev插件搭建基于Eclipse的python集成开发环境。
layout: post
slug: how-to-install-the-python-development-environment-with-eclipse
title: 搭建基于Eclipse的Python开发环境
categories:
- 脚本工具
tags:
- Python
---

* * *





  * 操作系统：LinuxMint13(或者Ubuntu12.04)

需要安装软件：  

  * Openjdk-6-jdk  

  * Python2.7  

  * Eclipse  

  * PyDev插件





# 安装openjdk-6-jdk




```sh    
sudo apt-get install openjdk-6-jdk
```


<!-- more -->



# 安装python2.7解释器





默认LinuxMint13和Ubuntu12.04都已经安装好了python2.7命令解释器。





# 安装Eclipse




    
```sh    
sudo apt-get insttall eclipse
``` 





# 安装PyDev插件





启动Eclipse，在Help菜单中选择"InstallNewSoftware"  

点击"Add"按钮，在弹出的"Add Repository"对话框中，添加PyDev的软件源地址




```
Name: PyDev  
Location: http://pydev.org/updates
```





软件源刷新之后，选择软件源内的"PyDev"选项下的"PyDevforEclipse"插件即可。  

之后点击安装。





在安装过程中，需要您确认是否信任该软件，选择信任后，继续安装。  

安装结束后，重启Eclipse。





![Eclipse安装1](/images/2012year/2012-06-08_EclipsNo.1.bmp)





![Eclipse安装2](/images/2012year/2012-06-08_EclipsNo.2.bmp)





# 配置PyDev插件所使用的Python解释器路径





打开Eclipse，在Window菜单中选择Preference。  

之后选择




```
PyDev-"Interpreter-Python"
```






新建一个Python解释器，填写内容如下：




```
Interpreter Name: Python2.7  
Interpreter Executable: /usr/bin/python
``` 




其他保持默认即可





# 测试





创建python项目进行测试。  

依次执行：




```
File-"New-Project"
```





选择PyDev下的PyDevProject，Grammer和Interpreter分别选择相应的版本。  

创建之后，随便写几行代码进行测试即可。





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



