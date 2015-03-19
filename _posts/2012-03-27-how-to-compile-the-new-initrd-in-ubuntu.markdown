---
author: chris.hill
comments: true
date: 2012-03-27
excerpt: 介绍在Ubuntu系统上制作新的initrd文件的方法步骤
layout: post
slug: how-to-compile-the-new-initrd-in-ubuntu
title: 在Ubuntu上制作新的initrd文件
categories:
- IT运维
tags:
- Linux
---

* * *





# 解压initrd软件包

```sh
mkdir initrd-tmp  
cd initrd-tmp  
gzip -dc /mnt/casper/initrd.gz | cpio -id
```




Ubuntu 9.10及之后的版本，可以执行:




```sh    
lzma -dc -S .lz /mnt/casper/initrd.lz | cpio -id
```


<!-- more -->

# 进入解压后的initrd文件里面



可以修改文件，或者添加驱动模块。



# 重新打包制作新的initrd文件

```sh
find . | cpio --quiet --dereference -o -H newc | gzip -9  ~/new-initrd.gz
```




或者_如果需要制作新的initrd.lz文件  

(for Ubuntu 9.10 and later)可以执行：

```sh
find . | cpio --quiet --dereference -o -H newc | lzma -7  ~/new-initrd.lz
```




* * *


作者：chris hill


微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



