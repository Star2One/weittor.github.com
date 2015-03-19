---
author: chris.hill
comments: true
date: 2012-02-15 
excerpt: 在CentOS系统上搭建RPM软件包编译环境
layout: post
slug: rpm-packages-compiled-guide-v1-0
title: RPM软件包编译指南v1.0
categories:
- IT运维
tags:
- Linux
---

* * *

#准备工作

##安装必备软件
gcc 编译的工具

  * rpm-build    
  * kernel-devel    
  * make    

执行命令：

```sh
yum install gcc rpm-build kernel-devel make -y
```


    注意事项
        1)在封装源码包前,必须保证源码包的代码可以正常通过编译，   
            即./configure -->make -->make install 没有报错    
        2)不要使用 root用户来编译rpm包。如果在编写过程中编译脚本报错,会直接影响系统的稳定性和安全性，推荐通过创建用户 rpmbuild，进行 rpm 软件包的编译工作。    
            添加rpmbuild
            用户执行命令：     
                useradd -m rpmbuild    
        3)编写好.spec配置脚本，必须使用 rpmbuild -b[...] spec-file 来测试脚本是否存在错误    


##设置环境变量

编译的过程中,系统会调用很多变量来对源码包进行封装和安装。   
这些变量存放在以下两个文件系统环境变量/usr/lib/rpm/macros用户自定义变量~HOME/.rpmmacros。    

    说明：
        自定义宏变量,存放在自己的家目录下。    
        在使用 rpmbuild 用户来封装RPM包的时候，系统会优先调用局部宏变量，再调用系统环境文件。    
        也就是说,当 rpmbuild用户封装 RPM 包的时候，系统会优先使用 /home/rpmbuild/.rpmmacros 文件里的变量，当这个文件的变量缺少的时候，会再去调用 /usr/lib/rpm/macros 文件里的变量。     
        一般我们需要配置用户自定义变量~HOME/.rpmmacros。    
        添加相应内容     
            vi .rpmmacros

##创建目录结构

编译rpm软件包需要事先创建好特定的目录结构。

首先查看目录系统默认的编译主目录。    
执行命令：

```sh
cat /usr/lib/rpm/macros|grep %_topdir
```

由上可知系统变量设定的编译主目录为编译用户家目录HOME下面的rpmbuild目录。   
故由于我们使用rpmbuild用户进行编译，故需要在rpmbuild用户家目录下面创建需要编译的目录结构。    
执行命令：

```sh
mkdir /home/rpmbuild/rpmbuild
cd /home/rpmbuild/rpmbuild
mkdir BUILD BUILDROOT RPMS SOURCES SPECS SRPMS
cd RPMS
mkdir athlon geode i386 i686 x86_64 noarch
```

<!-- more -->

###了解文件夹目录及文件类型

首先了解一下编译目录下的文件夹的作用及存放的文件类型。   

```sh
tree -C /home/rpmbuild/rpmbuild/
```

输出内容如下

```sh
tree
    /home/rpmbuild/rpmbuild/  
        ├── BUILD    
        ├── BUILDROOT   
        ├── RPMS  
        │   ├── athlon   
        │   ├── geode  
        │   ├── i386  
        │   ├── i686  
        │   ├── noarch  
        │   └── x86_64   
        ├── SOURCES   
        ├── SPECS   
        └── SRPMS   
```
说明
```
    文件夹   存放的文件     
    BUILD   源代码数据    
    RPMS   封装好的 RPM 包按平台放到指定的文件夹 *.rpm 包    
    SOURCES  源代码数据文件    
    SPECS  封装源码包的脚本文件*.spec 文件    
    SRPMS 封装好后,系统会把封装的脚本文件生成一个 *.src.rpm 包    
```

#编写*.spec 文件

现在我们开始编写一个*.spec 文件。    
sepc 文件是主要的将 SRPM 编译成 RPM 的设定档,它的基本规则为:    

  * 整个档案的开头以 Summary 为开始,这部分的设定都是最基础的说明内容。    
  * 然后每个不同的段落之间,都以%来作为开头,定义源代码打包封装的内容,例如%prep 与%install。

该文件整体信息如下

```
    Summary:主要的套件说明;    
    Name:这个就是套件的名称;    
    Version:这个是套件的版本信息;    
    Release:这个是该版本打包的次数说明;   
    Copyright:这个套件的授权模式,我們是使用 GPL 啦!    
    Group:这个套件的开发团体名称;   
    Source:这个套件的来源,即源代码目录地址!    
    Url:这个套件源代码的主要官方网站;    
    Packager:这个套件是由谁打包的    
    Vender:开发的厂商;   
    ExclusiveArch:这个是说明这个套件的实话安裝的硬件,通常默认为 i386   
    Requires:该套件所依赖的软件包    
```
    
定义源代码封装的信息

```
    源码包   
        |–定义属性 | 使用 rpm -qi package 可查看得到的属性    
        |–源码包封装 |    
        |–%prep   
        | 安装前的要求。即在尚未进行设定或安装之前,检查需   
        要进行的准备工作是否满足需要。prep 就是 prepare 的简写。该部分主要的工作事项有:   
        查找套机所需要的目录是否已经存在;事先建立套件所需要的目录,或者事先需要进行的任   
        务;如果待安装的 linux 系统内已经有安装的时候可能会被覆盖掉的档案时,那么就必须   
        要进行备份的工作   
        |—%setup     
        |   
        该段落就是我们 Tarball 源码中的那个 Makefile 档  
        案。所以就是执行./config 之类的设定文档。如果需要自己增加参数,就可以在这个地方   
        加入自己的设定值参数。如果没有这方面的需要,则可以不需要编写内容   
        |–%build   
        |  
        编译。通过 make 编译成为可执行的程序   
        |–%install  
        |  
        模拟安装。编译完成之后,就需要安装。安装就是写  
        在这里,类似于 tarball 里面 make install 的意思  
        |–%clean  
        |–%file  
        要写到这里  
        清除临时文件 和 垃圾文件  
        |   
        定义安装 (真正安装软件)。这个套件需要安装的文件都需  
        |
        | 使用 rpm -q –scripts package 可查看得到的属性   
        |–scriptlets   
        |–%pre   
        |   
        |–%post   
        |–%preun   
        安装前运行的脚本   
        |    
        |   
        |–%postun   
        安装后运行的脚本   
        卸载前运行的脚本   
        卸载前运行的脚本   
        |   
        |–%description   
        |–%changelog   
        |  
        |  
        软件包的描述信息,将套件做一个简短的说明   
        软件包的日志,更新信息   
        |–RPM 签名   
```


由于现在开源软件的tar.gz或者tar.bz2源码包里普遍都有软件作者编写好的一个模板.spec配置文件。    
因此我们就不需要手动自己创建该文件，直接修改作者提供的.spec文件即可。

接下来，我们以ethtool软件为例，来演示一下该软件的RPM包编译过程。



#开始编译


##下载ethtool源码包

```sh
wget http://www.kernel.org/pub/software/network/ethtool/ethtool-2.6.37.tar.gz
```


输出内容如下

```
    ....  
    2012-02-15 22:00:45 http://www.kernel.org/pub/software/network/ethtool/ethtool-2.6.37.tar.gz   
    2012-02-15 22:00:47 (128 KB/s)已保存;ethtool-2.6.37.tar.gz [183975/183975])
```

##解压该软件包，获取.spec文件

```sh
tar xvzf ethtool-2.6.37.tar.gz
cd ethtool-2.6.37
ls *.spec
ethtool.spec
```

拷贝该文件到编译目录/home/rpmbuild/rpmbuild/SPECS

```sh
cp ethtool.spec  /home/rpmbuild/rpmbuild/SPECS
```

拷贝ethtool源文件到编译目录/home/rpmbuild/rpmbuild/SOURCES

```sh
cp ethtool-2.6.37.tar.gz  /home/rpmbuild/rpmbuild/SOURCES
```

##查看ethtool.spec文件

看是否有需要修改的内容

```sh
cd /home/rpmbuild/rpmbuild/SPECS
vi ethtool.spec
```
编辑内容如下

```    
    Name : ethtool  
    Version : 2.6.37   
    Release : 1   
    Group : Utilities   
    Summary : A tool for setting ethernet parameters  
    License : GPL   
    URL : http://sourceforge.net/projects/gkernel/   
    Buildroot : %{_tmppath}/%{name}-%{version}   
    Source : %{name}-%{version}.tar.gz   
    %description   
    Ethtool is a small utility to get and set values from your your ethernet
    controllers. Not all ethernet drivers support ethtool, but it is getting
    better.   
    %prep   
    %setup -q  
    %build   
    CFLAGS=${RPM_OPT_FLAGS} ./configure prefix=/usr mandir=%{_mandir}   
    make  
    %install  
    make install DESTDIR=${RPM_BUILD_ROOT}   
    %files  
    %defattr(-,root,root)   
    /usr/sbin/ethtool  
    %{_mandir}/man8/ethtool.8*   
    %doc AUTHORS COPYING INSTALL NEWS README ChangeLog   
    %changelog  
```

提示：通常需要修改的是Source变量，其他的为默认值即可。


##使用 rpmbuild 进行打包

编译.spec脚本终于完成了,我们再次使用 rpmbuild 来编译软件包。

###rpmbuild命令选项

```
rpmbuild -b[...] specfile 的参数

- -bp 对%prep 进行检测
- -bc 对%build 进行检测
- -bi 对%install 进行检测
- -bb 只建立 2 进制代码包
- -bs 只建立源代码包
- -ba 把以上的过程全部运行一次
```
 
建议以下面的命令顺序对.spec配置文件以此进行测试，并最终编译

```sh
rpmbuild -bp ethtool.spec
rpmbuild -bc ethtool.spec
rpmbuild -bi ethtool.spec
```

若以上三个命令返回值为0，则说明以上编译测试命令运行成功，可以进行真正的软件包编译工作运行。
以下命令即可生成rpm软件包。

```sh
rpmbuild -ba ethtool.spec
```

执行过程信息

```sh
cd /home/rpmbuild/rpmbuild/BUILD
cd ethtool-2.6.37
/bin/rm -rf /home/rpmbuild/rpmbuild/BUILDROOT/ethtool-2.6.37-1.x86_64
exit 0
```

##查看RPMS目录下生成的rpm软件包

```sh
ls ../RPMS/x86_64/
```

ethtool-2.6.37-1.x86_64.rpm
软件包生成后，即可使用rpm命令查询该rpm包的详细信息

```sh
rpm -qpi ethtool-2.6.37-1.x86_64.rpm
```

列出 RPM 软件包的描述信息
```    
    Name : ethtool Relocations: (not relocatable)   
    Version : 2.6.37 Vendor: (none)    
    Release : 1 Build Date: 2012年02月15日 星期三 22时12分22秒   
    Install Date: (not installed) Build Host: localhost.localdomain   
    Group : Utilities Source RPM: ethtool-2.6.37-1.src.rpm   
    Size : 269404 License: GPL   
    Signature : (none)   
    URL : http://sourceforge.net/projects/gkernel/   
    Summary : A tool for setting ethernet parameters   
    Description :   
    Ethtool is a small utility to get and set values from your your ethernet
    controllers. Not all ethernet drivers support ethtool, but it is getting
    better. If your ethernet driver doesn't support it, ask the maintainer to
    write support it's not hard!
```


```sh
rpm -qpl ethtool-2.6.37-1.x86_64.rpm
```

列出 RPM 软件包内的文件信息

```
    /usr/sbin/ethtool  
    /usr/share/doc/ethtool-2.6.37   
    /usr/share/doc/ethtool-2.6.37/AUTHORS   
    /usr/share/doc/ethtool-2.6.37/COPYING   
    /usr/share/doc/ethtool-2.6.37/ChangeLog   
    /usr/share/doc/ethtool-2.6.37/INSTALL  
    /usr/share/doc/ethtool-2.6.37/NEWS  
    /usr/share/doc/ethtool-2.6.37/README   
    /usr/share/man/man8/ethtool.8.gz  
```

##使用yum命令安装该软件，并测试该软件


```sh
yum localinstall ethtool-2.6.37-1.x86_64.rpm
```

安装成功后通过root用户使用该命令查看网卡的相关信息

```sh
su
ethtool eth0
```


输出信息如下：

```
    Settings for eth0:  
    Supported ports: [ TP ]   
    Supported link modes: 10baseT/Half 10baseT/Full   
    100baseT/Half 100baseT/Full   
    1000baseT/Full  
    Supports auto-negotiation: Yes   
    Advertised link modes: 10baseT/Half 10baseT/Full   
    100baseT/Half 100baseT/Full  
    1000baseT/Full  
    Advertised pause frame use: No  
    Advertised auto-negotiation: Yes   
    Speed: 1000Mb/s   
    Duplex: Full   
    Port: Twisted Pair   
    PHYAD: 0   
    Transceiver: internal   
    Auto-negotiation: on  
    MDI-X: Unknown  
    Supports Wake-on: umbg  
    Wake-on: d  
    Link detected: yes  
```

由上可知该命令正常输出网卡的相关信息，故说明该软件包安装成功。



* * *





作者：chris hill   
微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



