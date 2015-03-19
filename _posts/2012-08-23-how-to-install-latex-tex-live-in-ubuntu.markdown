---
author: chris.hill
comments: true
date: 2012-08-23
excerpt: LaTex（读音/ˈleɪtɛx/或/ˈlɑːtɛx/，音译“拉泰赫”）是一种基于TEX的排版系统，由美国电脑学家莱斯利·兰伯特（Leslie Lamport）在20世纪80年代初期开发，利用这种格式，即使使用者没有排版和程序设计的知识也可以充分发挥由TEX所提供的强大功能，能在几天，甚至几小时内生成很多具有书籍质量的印刷品。本文为你介绍如何在ubuntu系统上安装LaTex软件。
layout: post
slug: how-to-install-latex-tex-live-in-ubuntu
title: 在Ubuntu上安装LaTex (Tex Live)
categories:
- IT运维
tags:
- Linux
---

* * *





LaTex（读音/ˈleɪtɛx/或/ˈlɑːtɛx/，音译“拉泰赫”）是一种基于TEX的排版系统，由美国电脑学家莱斯利·兰伯特（Leslie Lamport）在20世纪80年代初期开发，利用这种格式，即使使用者没有排版和程序设计的知识也可以充分发挥由TEX所提供的强大功能，能在几天，甚至几小时内生成很多具有书籍质量的印刷品。  

对于生成复杂表格和数学公式，这一点表现得尤为突出。  

因此它非常适用于生成高印刷质量的科技和数学类文档。





这个系统同样适用于生成从简单的信件到完整书籍的所有其他种类的文档。






在众多LaTex软件中，我们选择在Ubuntu系统上安装强大的LaTex发行版Tex Live。

<!-- more -->

执行命令：

    
    
```sh
sudo apt-get install texlive-full
```




安装LaTex编辑器Texmaker执行命令：




    

```sh    
sudo apt-get install texmaker
```





打开Texmaker编辑器进行测试




    
    
```sh
texmaker
```





点击“文件->新建”创建测试文档，添加如下内容：




    
    
```
\documentclass{article}
\begin{document}
Welcome to weittor's Blog
Site:www.weittor.com
\end{document}
```





之后点击“快速构建”即可生成默认的pdf文档weittor.pdf





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



