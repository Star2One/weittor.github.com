---
author: chris.hill
comments: true
date: 2014-06-01
excerpt: kickstart配置文件生成密码
layout: post
slug: how-to-generate-rootpw
title: kickstart配置文件生成密码
categories:
- 运维自动化
tags:
- Cobbler
---

* * *

配置文件目录`/var/lib/cobbler/kickstarts`

配置文件`default.ks`


默认的root密码可以通过kickstarts配置文件的`rootpw`选项指定。

`rootpw密码部分`可以使用如下命令生成

```bash
perl -e 'print crypt("123456", "\$1\$fmI8lgbE\$"), "\n" '
```
生成的加密密码如下：

```
$1$fmI8lgbE$RIZzEGcR8SnIWA6/A0dGL0
```

注：使用`grub-md5-crypt`命令来生成也可以。


* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

