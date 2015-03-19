---
author: chris.hill
comments: true
date: 2012-03-30
excerpt: 介绍在Ubuntu系统上搭建属于自己的deb软件包编译环境
layout: post
slug: simple-deb-packages-compiled-guide-v1-0
title: 简单的deb软件包编译指南v1.0
categories:
- IT运维
tags:
- Linux
---

* * *





  * 操作系统：Ubuntu 10.04 LTS



# 安装开发软件




```sh    
dpkg-dev file gcc g++ libc6-dev patch make perl autoconf automake dh-makedebhelper devscripts fakeroot gnupg xutils lintian pbuilder debian-policy developers-reference
```



# 准备应用程序源代码





_chris.hill以打包ethtool软件包为例进行说明_





下载ethtool源码包ethtool-2.6.36.tar.gz  

查看该软件包是否存在




    
```sh 
ls /home/chris/debbuild/ethtools/ethtool-2.6.36.tar.gz
```

输出如下：    
    

```
/home/chris/debbuild/ethtools/ethtool-2.6.36.tar.gz
```

解压源码包


```sh    
cd /home/chris/debbuild/ethtools/
tar xvzf ethtool-2.6.36.tar.gz... 
ls
```

输出如下：
    
```    
ethtool-2.6.36  
ethtool-2.6.36.tar.gz
``` 





进入ethtool-2.6.36 这个目录并且彻底的读完其中的文档。  

通常情况下在目录里面会有名叫README_、INSTALL_、_.lsm或者_.html的文件。  

你必需找到如何正确编译并安装程序的安装说明文档。



<!-- more -->



安装过程对于不同的软件是不同的，但很多现代的程序都带有一个“configure” 脚本文件，这个文件配制你系统上的源文件，并确认你的系统已经可以编译它了。  

在通过“./configure”命令配置之后，通常可以通过“make”来编译程序。  

有一些程序还会支持通过“make check”命令来进行自检。  

把程序安装到目标目录中的命令通常是“make install”。





现在可以试着编译并运行你的程序了，从而确定它可以很好的工作并且在它安装或工作时不会破坏其它程序的运行。  

另外，通常你还可以通过“make clean”(或者更好的“make distclean”)命令来清理build目录。有时还会有一个“make uninstall”命令来删除所有已经安装的文件。





# 首次Debian化





确定你在程序的原代码目录中，然后执行这个命令：




```sh 
cd ethtool-2.6.36/
dh_make -e your.maintainer@address -f ../ethtool2.6.36.tar.gz
```


输出如下：
    
```    
Type of package: single binary, indep binary, multiple binary, library, kernel module, kernel patch or cdbs?[s/i/m/l/k/n/b] **s** *你需要创建那种类型的软件包*
    
```




这里我们输入“s”键选择第一个选项，只创建一个二进制形式的软件包。




    
```    
    Maintainer name : weittor
    EmailAddress : your.maintainer@address
    Date : Fri, 30 Mar 2012 16:51:40 +0800
    Package Name : ethtool
    Version : 2.6.36
    License : blank
    Using dpatch : no
    Using quilt : no
    Type of Package : Single
    Hit enter to confirm:     *查看屏幕上的信息，然后按enter键确认*
    Done. Please edit the files in the debian/ subdirectory now. ethtooluses a configure script, so you probably don't have to edit the Makefiles.
```    





当然，要用你的E-mail地址换掉字符串“your.maintainer@address”，并用你的源代码文档的名字替换掉上面的文件名。  

你的这个E-mail地址将会被包含在changlog 项目和其它的文件中。  

在此次运行dh_make之后，上游的软件包将会被打包为ethtool_2.6.36.orig.tar.gz并放在父目录中，以便用diff.gz创建非Debian固有的源代码包。





> 请注意：你只能运行一次dh_make程序，如果你再次在同一个已经“Debian化”的目录中运行它，它将不能正常运行






# 构建软件包





完整的rebuild进入程序的主目录然后运行如下命令



```sh 
dpkg-buildpackage -r fakeroot
```



这将为你做好每件事情。它会：







  * 清理源代码目录树(debian/rules clean)，需要使用fakeroot    


  * 构建源代码软件包(dpkg-source -b)   


  * 构建程序(debian/rules build)    


  * 构建二进制软件包(debian/rulesbinary)，需要使用fakeroot    


  * 用文件.dsc给源代码签名，需要使用gnupg    


  * 创建上传文件.changes并给它签名，需要使用 dpkg-genchanges和gnupg    


  * 唯一可能需要你输入的是你的GPG密钥的密码，两次    





当完成所有这些，你会在上一层目录(~)中看到下面的文件：







  * ethtool_2.6.36.orig.tar.gz   





这是原来的源程序的压缩包，为了遵守Debian的标准，修改了它的文件名。注意它是我们通过在开始时运行带有“-f”参数的dh_make命令创建的。







  * ethtool_2.6.36-1.dsc





这是对源代码的一个总结。这个程序是利用你的“control”文件创建的，并且在用dpkg-source(1)命令解包源代码时将会用到。这个文件已经有了GPG签名，这样人们就可以确认他是你发布的。







  * ethtool_2.6.36-1.diff.gz





这个文件中包含了每一个你对原始源代码所做的每一个修改，它的格式是“unified diff”。他是由dpkg-source(1)程序创建的，而且这个程序还要使用它。







  * ethtool_2.6.36-1_amd64.deb





这是你的完整的二进制软件包。你可以象对待其它软件包一样用dpkg命令安装和删除它。







  * ethtool_2.6.36-1_amd64.changes





这个文件描述了描述了所有对当前版本的修订版所作的改动，Debian FTP文档维护程序在安装二进制版本软件包和源代码版本的软件包时将会使用到它。  

它的一部分是通过“changelog”文件和.dsc文件创建的。  

这个文件已经有了GPG签名，这样人们可以确信它确实是你的。因为你会继续花精力在这个软件包上，它的行为可能会改变，还有可能会增加一些新的功能。  

下载了你的软件包的人们可以通过阅读这个文件从而快速的了解到什么东西发生了变化。Debian的文档维护程序也会把这个文件的内容发送到debian-devel-changes邮件列表上。





.dsc和.changes文件中的长数字字符串是上面提到的文件的MD5校验码。  

下载了你的文件的人可以用md5sum(1)来检查这些数字是否相同，这样它们就可以知道文件是不是损坏了，或者是否被窜改了。





# 安装测试



```sh
dpkg -i ethtool_2.6.36-1_amd64.deb
```



* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



