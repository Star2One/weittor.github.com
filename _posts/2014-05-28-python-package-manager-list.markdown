---
author: chris.hill
comments: true
date: 2014-05-28
excerpt: Linux系统内的Python软件包管理工具.
layout: post
slug: python-package-manager-list
title: Linux系统内的Python软件包管理工具
categories:
- 程序设计
tags:
- Python
---

* * *

#概括
常用的Python软件包管理工具包括：

* Pip      
<https://pypi.python.org/pypi/pip>

* setuptools    
<https://pypi.python.org/pypi/setuptools>

* distribute    
<https://pypi.python.org/pypi/distribute>


#Pip
官方简介    
``
A tool for installing and managing Python packages
``

##安装pip

###方法一
通过python脚本安装

官方脚本地址    
<https://raw.github.com/pypa/pip/master/contrib/get-pip.py>

通过管理员执行该脚本

```sh
python get-pip.py
```
通过该方法可以获得最新的pip版本。

<!-- more -->

###方法二   
通过系统管理工具安装。    

* debian和ubuntu系统

```bash
sudo apt-get install python-pip
```

* redhat和centos系统

```
sudo yum install python-pip
```

##使用方法介绍
通过pip安装python软件包  

```
pip install SomePackage
```

通过pip卸载python软件包  

```
pip uninstall SomePackage
```
查看过期的软件包

```sh
pip list --outdated
```
> 提示：若执行该命令出现`No command by the name pip list`的报错
请用get-pip.py脚本安装最新版的pip

升级Python软件包

```sh
pip install --upgrade SomePackage
```
查看哪些文件将要被安装

```sh
pip show --files SomePackage
```

#distribute
官方简介   
`Easily download, build, install, upgrade, and uninstall Python packages`

该软件包基于setuptools进行了重构。
用法与setuptools类似。



#setuptools

官方简介    
`Easily download, build, install, upgrade, and uninstall Python packages`

##安装方法
###方法一
通过官方提供的脚本安装。

* root用户安装   

```sh
wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py -O - | python
```

* 非root用户执行sudo安装    

```sh
wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py -O - | sudo python
```

###方法二
通过系统管理工具安装。

* debian和ubuntu系统

```sh
sudo apt-get install python-setuptools
``` 

* redhat和centos系统

```sh
sudo yum install python-setuptools
```



#参考资料

<http://stackoverflow.com/questions/12344290/pip-error-no-command-by-the-name-pip-uninstall>















* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



