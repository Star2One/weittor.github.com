---
author: chris.hill
comments: true
date: 2013-08-27
excerpt: 默认bash解释器会持续等待用户输入，可通过设置TMOUT变量，来指定用户操作的超时时间，提供用户终端的安全性。
layout: post
slug: automatically-logout-idle-bash-session
title: '[How-To]设置bash会话超时主动注销'
categories:
- 脚本工具
tags:
- Shell
---

* * *







#目的





设置bash会话超时主动注销。







#方法





通过设置Bash终端会话的超时时间，自动注销当前闲置用户会话。




<!-- more -->


# 步骤





默认bash解释器会持续等待用户输入，可通过设置TMOUT变量，来指定用户操作的超时时间，提供用户终端的安全。  

1)创建/etc/profile.d/bash_autologout.sh文件




    
    
```sh
touch etc/profile.d/bash_autologout.sh
```   





2)添加命令"export TMOUT=600"到该文件




    
```sh
echo "export TMOUT=600" >>/etc/profile.d/bash_autologout.sh
```





3)重新登陆查看变量是否设置成功







#参考资料





[http://www.debuntu.org/how-to-automatically-logout-idle-bash-session](http://www.debuntu.org/how-to-automatically-logout-idle-bash-session)


-------


作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



