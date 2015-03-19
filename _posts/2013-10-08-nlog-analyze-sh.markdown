---
author: chris.hill
comments: true
date: 2013-10-08
excerpt: 分析nginx日志脚本nlog-analyze.sh
layout: post
slug: nlog-analyze-sh
title: 分析nginx日志脚本nlog-analyze.sh
categories:
- 脚本工具
tags:
- Shell
---

* * *





README




    1. 分析nginx访问日志access.log
    2. 统计内容如下
        * google\baidu\bing爬虫程序访问统计情况
        * 浏览器访问统计情况
        * 桌面操作系统访问统计情况
        * 智能移动终端访问统计情况
        * 其他访问统计情况



**Note:**
后期_chris hill_将会根据实际情况对该脚本进行功能扩展与更新，欢迎继续关注





将该脚本放在access.log目录，执行命令




    
```sh
bash nginx-log.sh
```


<!-- more -->

获取结果如下




    
    
    Log access.log Count: 1464665 lines
    From IP Count：47459
    ########################
    Http Web Status Analyze
    ########################
    73.976% http 200: 1083499
    0.004%  http 206: 53
    0.360%  http 404: 5270
    0.000%  http 503: 0
        
    ########################
    Spiders Status Analyze
    ########################
    0.048%  From Google Spiders: 699
    0.186%  From Baidu Spiders: 2730
    0.042%  From Bing Spiders: 620
        
    ########################
    Browers Status Analyze
    ########################
    47.153% MSIE: 690638
    0.923%  Firefox: 13513
    24.252% Webkit: 355205
    0.093%  Opera: 1365
        
    ########################
    Desktop Status Analyze
    ########################
    65.818% Windows: 964019
    0.205%  Mac OS: 3009
    0.110%  Linux with X11: 1617
        
    ########################
    Smartphone Status Analyze
    ########################
    0.036%  Nokia Series: 528
    0.053%  Android: 780
    1.455%  iPad: 21316
    1.115%  iPhone: 16334
    0.016%  Windows Phone: 236
        
    ########################
    Others Status Analyze
    ########################
    0.000%  access mp3 file: 0
    0.021%  access jpg file: 301
    





有需要的童鞋，可通过以下地址下载
[hava fun!](http://pan.baidu.com/s/15kEBV)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



