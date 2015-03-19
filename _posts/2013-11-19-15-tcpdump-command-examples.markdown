---
author: chris.hill
comments: true
date: 2013-11-19 
excerpt: 'tcpdump作为网管必备利器之一，是网络分析排错的重要工具。

  本文为大家介绍15个常用的tcpdump命令。'
layout: post
slug: 15-tcpdump-command-examples
title: 15个常用的tcpdump命令
categories:
- IT运维
tags:
- Linux
---

* * *





tcpdump作为网管必备利器之一，是网络分析排错的重要工具。





下面为大家介绍15个常用的tcpdump命令。





# tcpdump命令举例





1.分析特定网卡的网络流量




```sh 
tcpdump -i eth0
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    21:23:58.456673 IP WeittorLab.ssh > 192.168.1.110.51335: Flags [P.], seq 3194195677:3194195873, ack 3364749995, win 141, length 196
    21:23:58.456721 IP WeittorLab.ssh > 192.168.1.110.51335: Flags [P.], seq 196:248, ack 1, win 141, length 52
    21:23:58.457461 IP WeittorLab.51407 > 192.168.1.1.domain: 64393+ PTR? 110.1.168.192.in-addr.arpa. (44)
    3 packets captured
    47 packets received by filter
    14 packets dropped by kernel


<!-- more -->



2.捕获指定数量的数据




    
```sh
tcpdump -c 3 -i eth0
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    21:24:43.098599 IP WeittorLab.ssh >> 192.168.1.110.51335: Flags [P.], seq 3194197349:3194197545, ack 3364750583, win 141, length 196
    21:24:43.098645 IP WeittorLab.ssh >> 192.168.1.110.51335: Flags [P.], seq 196:248, ack 1, win 141, length 52
    21:24:43.099396 IP WeittorLab.39700 >> 192.168.1.1.domain: 23487+ PTR? 110.1.168.192.in-addr.arpa. (44)
    3 packets captured
    43 packets received by filter
    10 packets dropped by kernel





3.显示ASCII格式的数据包




    
```sh
tcpdump -A -i eth0
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
    12:13:59.813814 IP weittor-inc.com.ssh > 192.168.10.27.51524: P 3515008196:3515008392(196) ack 1859193307 win 30
    E.....@.@...
    @........D....n. .P... D...}.ySL.....U.......:.=.+Z5.My
    |.{G..T.....
    12:13:59.814336 IP weittor-inc.com.7118 > 210-inc.com.domain: 5268+ PTR? 27.10.168.192.in-addr.arpa. (44)
    E..H%<@.@.|.
    @.
     C....5.4...............27.10.168.192.in-addr.arpa.....
    2 packets captured
    9 packets received by filter
    0 packets dropped by kernel




4.显示十六进制和ASCII格式的数据包




    
```sh
tcpdump -XX -i eth0
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
    12:14:38.980356 IP weittor-inc.com.ssh>> 192.168.10.27.51524: P 3515010100:3515010296(196) ack >1859194295 win 30
         0x0000: 0cda 41af 2eeb 14fe b5d5 37d9 0800 4510 ..A.......7...E.
         0x0010: 00ec 1ced 4000 4006 fda9 0a0a 4098 c0a8 ....@.@.....@...
         0x0020: 141b 0016 c944 d182 cc34 6ed1 0db7 5018 .....D...4n...P.
         0x0030: 001e 2044 0000 a7de 751f b39b 2bc9 2bb3 ...D....u...+.+.
         0x0040: b9cb c5d8 d45d a0aa c079 1a11 d612 e8b3 .....]...y......
         0x0050: 7504 7087 9f6b 2c95 f6a5 4734 7076 4d2f u.p..k,...G4pvM/
    12:14:38.980881 IP weittor-inc.com.56945>> 210-inc.com.domain: 27553+ PTR? 27.10.168.192.in-addr.arpa. (44)
         0x0000: b63e 9b30 e26e 14fe b5d5 37d9 0800 4500 .>.0.n....7...E.
         0x0010: 0048 be39 4000 4011 e3ed 0a0a 4098 0a0a .H.9@.@.....@...
         0x0020: 43d2 de71 0035 0034 98c3 6ba1 0100 0001 C..q.5.4..k.....
         0x0030: 0000 0000 0000 0232 3702 3230 0331 3638 .......27.20.168
         0x0040: 0331 3932 0769 6e2d 6164 6472 0461 7270 .192.in-addr.arp
         0x0050: 6100 000c 0001 a.....
    2 packets captured
    10 packets received by filter
    0 packets dropped by kernel



5.将捕获的数据包信息重定向到特定的文件中




    
```sh
tcpdump -w 11192013.pcap -i eth0
```




输出





    tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    80 packets captured
    80 packets received by filter
    0 packets dropped by kernel



6.读取指定的数据包文件




    
```sh
tcpdump -tttt -r 22192013.pcap
```




输出





    2013-11-19 12:18:57.534109 IP weittor-inc.com.49302 > 6-85.com.4506: Flags [S], seq 2707142348, win >14600, options [mss 1460,sackOK,TS val 86058098 ecr 0,nop,wscale 7], length 0
    2013-11-19 12:18:57.535263 IP weittor-inc.com.4506 > 6-93.com.49302: Flags [R.], seq 0, ack 2707142349, win 0, length 0
    2013-11-19 12:18:57.656032 ARP, Request who-has 67-221.bjyz.dajie-inc.com tell , length 46






7.禁用DNS解析




    
```sh
tcpdump -n  -c 3 -i eth0
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    21:26:29.310735 IP 192.168.1.100.22 > 192.168.1.110.51335: Flags [P.], seq 3194203929:3194204125, ack 3364751879, win 141, length 196
    21:26:29.310986 IP 192.168.1.100.22 > 192.168.1.110.51335: Flags [P.], seq 196:376, ack 1, win 141, length 180
    21:26:29.311063 IP 192.168.1.100.22 > 192.168.1.110.51335: Flags [P.], seq 376:540, ack 1, win 141, length 164
    3 packets captured
    4 packets received by filter
    0 packets dropped by kernel




8.以适合可读的时间戳格式显示

    
```sh
tcpdump -n -tttt -i eth0
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    2013-11-19 21:27:11.116837 IP 192.168.1.100.22 > 192.168.1.110.51335: Flags [P.], seq 3194889605:3194889801, ack 3364753871, win 141, length 196
    2013-11-19 21:27:11.117191 IP 192.168.1.100.22 > 192.168.1.110.51335: Flags [P.], seq 196:392, ack 1, win 141, length 196
    2013-11-19 21:27:11.117317 IP 192.168.1.100.22 > 192.168.1.110.51335: Flags [P.], seq 392:556, ack 1, win 141, length 164
    3 packets captured
    4 packets received by filter
    0 packets dropped by kernel






9.只读取大于N bytes的数据包信息




    
```sh
tcpdump -w g_1024.pcap greater 1024
```




10.只捕获特定协议的数据包




    
```sh
tcpdump -i eth0 arp
```




输出





    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    21:28:14.934292 ARP, Request who-has 192.168.1.1 tell 192.168.1.102, length 46
    21:28:14.966837 ARP, Request who-has 192.168.1.1 tell 192.168.1.102, length 46
    21:28:15.000800 ARP, Request who-has 192.168.1.1 tell 192.168.1.102, length 46
    3 packets captured
    5 packets received by filter
    0 packets dropped by kernel






11.只读取小于N bytes大小的数据包




    
```sh
tcpdump -w l_1024.pcap less 1024
```




12.捕获特定端口的数据包




    
```sh
tcpdump -i eth0 port 22
```






13.捕获特定目的地址和端口的数据包




    
```sh
tcpdump -w weittor-packets.pcap -i eth0 dst 10.191.14.16 and port 22
```









14.捕获两台服务器之间的TCP协议数据包




    
```sh
tcpdump -i eth0 tcp and dst 192.168.1.100 and src 192.168.1.110 tcp
```








15.排除特定协议的数据包




    
```sh
tcpdump -i eth0 not arp and not rarp
```







# 参考资料





[thegeekstuff](http://www.thegeekstuff.com/2010/08/tcpdump-command-examples/)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



