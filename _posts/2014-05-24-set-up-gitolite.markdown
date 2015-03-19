---
author: chris.hill
comments: true
date: 2014-05-24 
excerpt: 在CentOS系统上搭建gitolite服务。
layout: post
slug: set-up-gitolite
title: CentOS系统搭建gitolite服务
categories:
- 脚本工具
tags:
- Git
---

* * *





CentOS系统搭建gitolite服务

#准备工作

>客户端IP    192.168.20.26    
>服务端IP    192.168.20.120

#安装软件
##服务端执行

```bash
yum perl-Time-HiRes openssh-server perl 
yum -y install git
```
##客户端执行##

```bash
yum -y install git
```
#添加用户
##服务端执行

```bash
groupadd git
useradd -g git -m  git
```
设置密码 

```bash
passwd git
```

<!-- more -->


#拷贝公钥
Gitolite管理的方式是给你一个特殊的仓库，修改，提交，推送到服务器就可以了。    
这个仓库只可以管理员访问，先把管理员的公钥复制到服务器上(可以和Git服务器在同一台服务器上也可以在不同的服务器上)。
##客户端执行
生成公钥weittor.pub，妥善保管好私钥weittor

```bash
cd ~/.ssh/
ssh-keygen -f weittor
```

拷贝管理员公钥weittor.pub到git服务器上

```bash
ssh-copy-id -i weittor.pub git@192.168.20.120
```
 
#安装Gitolite
##服务端执行
在Git服务器上切换到git用户进行安装。   
切换到git用户

```bash
su - git
```
 
创建gitolite安装目录

```bash
mkdir bin
```
重命名管理员公钥weittor.pub

```bash
mv ~/.ssh/authorized_keys ~/weittor.pub
```
提示：authorized_keys为前一步通过ssh-copy-id命令传输过来的weittor.pub公钥

从github上获取gitolite最新的源代码

```bash
git clone git://github.com/sitaramc/gitolite.git
```
安装到~/bin目录

```bash
~/gitolite/install -to ~/bin
```

初始化gitolite的git库

```bash
~/bin/gitolite setup -pk ~/weittor.pub
```
安装已经完成了，可以再以管理用户进行测试(前面运行ssh-copy-id的服务器)
 
#测试
##客户端执行

```bash
ssh git@192.168.20.120
```
应该看到类似这样的输出:

> 
PTY allocation request failed on channel 0    
hello weittor, this is git@gitserver running gitolite3 v3.5.3.1-13-gb23aed9 on git 1.7.1    
            R W gitolite-admin   
            R W testing    
Connection to 192.168.20.120 closed.   

 

可以把管理仓库克隆下来管理Git服务器

```bash
git clone git@192.168.20.120:gitolite-admin.git
```
要添加用户，只要把用户的公钥复制到这个仓库里的keydir下并以username.pub命名，然后提交并推送到服务器。

##添加用户示例(james)

```bash
cd gitolite-admin
cp ~/.ssh/james.pub keydir/james.pub
git add keydir
git commit -m "add new user james"
git push
```
 
##创建仓库示例
创建一个名为wt-shell的仓库，james有读写权限

```bash
cd gitolite-admin
vi conf/gitolite.conf
```
添加类似下面这内容进去

```
repo wt-shell    
    RW+    =   james
```
>注释：赋予用户james以下权限R(读)、W(写)、+(强制推送)

保存，提交并提交到git服务端

```bash
git add conf
git commit -m 'add new repo wt-shell and assign RW+ to james'
git push
```
推送的时候应该看到类似这样的信息
> 
Counting objects: 7, done.    
Delta compression using up to 4 threads.   
Compressing objects: 100% (3/3), done.    
Writing objects: 100% (4/4), 395 bytes, done.   
Total 4 (delta 1), reused 0 (delta 0)   
remote: Initialized empty Git repository in /home/git/repositories/wt-shell.git/    
To git@192.168.20.120:gitolite-admin    
   6de90b8..52737aa  master -> master   

>注释：remote开头的一行说明，git已经帮你创建了wt-shell仓库

##通配符仓库的创建示例
>说明：通配符仓库事先不能确定名字，所以不会帮你创建，在你clone的时候才会创建。

编辑conf/gitolite.conf文件在里面加入类似下面的内容

```
repo sandbox/.+$    
  C      =   jack    
  RW+C   =   jack    
```
>注意:`C = username`的一行必不可少，这里的C是指创建仓库的意思，下一行的RW+C中的C是指创建引用(branch,tag)的意思。
 
保存后提交并推送到服务上去

```bash
git add conf
git commit -m 'add sandbox repo'
git push
```
>注释：再次查看push时输出的信息，应该没有创建仓库的信息。
 
这时jack克隆仓库的时候会自动创建。   
使用jack用户执行如下命令

```bash
git clone git@192.168.20.120:sandbox/test.git
```
输出应该类似这样
>
Cloning into 'test'...   
Initialized empty Git repository in /home/git/repositories/sandbox/test.git/  
warning: You appear to have cloned an empty repository.   

如果你的输出报这样的错
>
FATAL: R any sandbox/test jenny DENIED by fallthru  
(or you mis-spelled the reponame)   
fatal: The remote end hung up unexpectedly   

一般是因为没有`C=username`这一行，注意是只有C的一行。



#参考资料

<http://williamherry.com/blog/2012/10/03/install-gitolite/>     

<http://www.ginano.net/centos-gitolite-git-server>   

<http://www.bigfastblog.com/gitolite-installation-step-by-step>    

<http://blog.sina.com.cn/s/blog_6b61ec070101f44e.html>   

<http://www.ossxp.com/doc/git/gitolite.html#id5>   

<http://zengrong.net/post/1720.htm>   

<http://aeturnalus.com/software/redmine-git-hosting-setup-ubuntu-12-04-lts/>    

<http://roclinux.cn/?p=2553>   





* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



