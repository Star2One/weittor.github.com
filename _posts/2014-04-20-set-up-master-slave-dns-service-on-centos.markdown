---
author: chris.hill
comments: true
date: 2014-04-20 
excerpt: 实际应用环境中，需要应用服务都依赖DNS服务，本文将为大家介绍如何在CentOS系统上搭建主从DNS服务。
layout: post
slug: set-up-master-slave-dns-service-on-centos
title: CentOS系统搭建主从DNS服务
categories:
- Web应用
tags:
- DNS
---

* * *





_通过bind搭建DNS主从服务器_





>主DNS server配置
>> IP地址：192.168.122.101 

>> FQDN：122-101.weittor-inc.com  
>> 操作系统：Centos6.4

> 辅DNS server配置
>> IP地址：192.168.122.102 
>> FQDN： 122-102.weittor-inc.com  
>> 操作系统：Centos6.4

# 前期准备





## 安装centos





[略]





## 禁用selinux





[略]





## 禁用iptables




    
```bash
    /etc/init.d/iptables stop
    chkconfig  iptables off   //开机永久关闭
```





## 填写完整的/etc/hosts配置





[略]


<!-- more -->


# 主DNS服务器配置[Master DNS Server]





## 安装bind




    
```bash
    yum install bind-*
```





## 配置文件





### 配置文件概览





创建的配置文件列表如下




    
```bash
    ls /var/named
```





输出




    
```bash
    localtime      
    named.rfc1912.zones
    bak-named.online.zones
    named.conf     
    rndc.conf
    bind.keys          
    named.online.zones
    zones
```





### 编辑主配置文件named.conf




    
```sh
    options {
            listen-on port 53 { 127.0.0.1;192.168.122.101; };
            listen-on-v6 port 53 { ::1; };
            directory       "/var/named";   ##指定根目录
            dump-file       "/var/named/data/cache_dump.db";
            statistics-file "/var/named/data/named_stats.txt";
            memstatistics-file "/var/named/data/named_mem_stats.txt";
            allow-transfer {
                    127.0.0.1;
                    192.168.122.101;  
                    192.168.122.102;    ##指定辅DNS服务器
            };
     
            allow-query     {
                    127.0.0.1/8;
                    192.168.122.0/24;
                    };
     
            recursion yes;
            dnssec-enable yes;
            dnssec-validation yes;
            dnssec-lookaside auto;
     
    /* Path to ISC DLV key */
    #       bindkeys-file "/etc/named.iscdlv.key";
    #       managed-keys-directory "/var/named/dynamic";
    };
     
     
     
    key "rndc-key" {
            algorithm hmac-md5;
            secret "fUgKXu0t1woSwFfVB8xOTQ==";
    };
     
     
     
    controls {
            inet 127.0.0.1 port 953
            allow { 127.0.0.1; } keys { "rndc-key"; };
    };
     
     
     
    #logging {
    #        channel default_debug {
    #                file "data/named.run";
    #                severity dynamic;
    #        };
    #};
     
    #zone "." IN {
    #       type hint;
    #       file "named.ca";
    #};
     
    #include "/etc/named.rfc1912.zones";
    #include "/etc/named.root.key";
    logging{
            channel simple_log {
                    file "/var/log/bind.log" versions 5 size 50m;
                    severity info;
                    print-time yes;
                    print-severity yes;
                    print-category yes;
            };
     
            category default{
                    simple_log;
            };
     
            channel queries_log {
                    file "/var/log/bind_queries.log" versions 5 size 50m;
                    print-time yes;
                    print-severity yes;
                    print-category yes;
            };
     
            category queries {
                    queries_log;
            };
     
            category lame-servers {
                    null;
            };
    };
     
     
     
    zone "." IN {
            type hint; 
            file "named.ca";    ##域名根域配置文件
    };
     
    include "etc/named.online.zones";   ##指定在线域名配置文件
     
```
    





生成根域配置文件named.ca




    
```bash
    wget http://www.internic.net/domain/named.root -O named.ca
```





### 编辑线上域列表配置文件named.online.zones




    
```sh
    zone "weittor-inc.com" {
            type master;
            file "weittor-inc.com.zone";    ##正向域名配置文件
            allow-update {
                    none;
            };
            allow-transfer {
                127.0.0.1;
                192.168.122.101;
                192.168.122.102;
            };
            notify explicit;
            also-notify {
                127.0.0.1;
                192.168.122.101;
                192.168.122.102;
            };
     
        allow-update { none; };
    };
     
     
     
    zone "168.192.in-addr.arpa." {
        type master;
        file "168.192.in-addr.arpa.zone";   ##反向域名配置文件
        allow-transfer {
            127.0.0.1;
            192.168.122.101;
            192.168.122.102;
            };
     
            notify explicit;
            also-notify {
            127.0.0.1;
            192.168.122.101;
            192.168.122.102;
            };
     
        allow-update { none; };
    };
     
```





### 编辑zone分区文件





#### 编辑正向解析配置文件weittor.com.zone




    
```sh
    $ORIGIN .
    $TTL 600; 10 minutes
    weittor-inc.comIN SOAns1.weittor-inc.com. domain.weittor-inc.com. (
    2014040101 ; serial
    10800      ; refresh (3 hours)
    600        ; retry (10 minutes)
    604800     ; expire (1 week)
    60         ; minimum (1 minute)
    )
     
    $TTL 43200; 12 hours
    NS  ns1.weittor-inc.com.
    NS  ns2.weittor-inc.com.
    A   192.168.122.13
     
     
     
     
    $ORIGIN weittor-inc.com.
    $TTL 43200; 12 hours
    ;TYPE A
     
     
    ns1 A   192.168.122.101
    ns2 A   192.168.122.102
     
     
    $TTL 600; 10 minutes
    mx1 A   192.168.122.65
    mx2 A   192.168.122.66
     
    blog    A   192.168.122.132
    book    A   192.168.122.133
     
     
    $ORIGINn.weittor-inc.com.
    $TTL 600; 10 minutes
    ;TYPE A
     
    a   A   192.168.122.13
     
    $ORIGIN weittor-inc.com.
    $TTL 600         ; 10 minutes
    ;TYPE CNAME
     
     
    www CNAME   a.n.weittor-inc.com.
    tech    CNAME   a.n.weittor-inc.com.
```
    





#### 编辑反向解析配置文件168.192.in-addr.arpa.zone




    
```sh
    $ORIGIN .
    $TTL 600        ; 10 minutes
    168.192.in-addr.arpa      IN SOA  ns1.weittor-inc.com. domain.weittor-inc.com. (
                                    2014022001 ; serial
                                    10800      ; refresh (3 hours)
                                    600        ; retry (10 minutes)
                                    604800     ; expire (1 week)
                                    60         ; minimum (1 minute)
                                    )
     
                            NS      ns2.weittor-inc.com.
                            NS      ns1.weittor-inc.com.
     
    $ORIGIN 168.192.in-addr.arpa.
    $TTL 600      ; 1 hour
     
    65.122                   PTR     122-65.weittor-inc.com.
    66.122                   PTR     122-66.weittor-inc.com.
    13.122                   PTR     122-13.weittor-inc.com.
    132.122                   PTR     122-132.weittor-inc.com.
    133.122                   PTR     122-133.weittor-inc.com.
```
    





## 启动服务





### 检查配置文件是否正确





使用命令named-checkconf有关配置




    
```bash
    named-checkzone weittor-inc.com weittor-inc.com.zone
```





检查配置文件正确的话，启动服务。





### 启动进程




    
```bash
    service named restart
    chkconfig named on //开机开启
```





### 测试




    
```bash
    nslookup www.weittor-inc.com
```





//查询到正确地址ok的话，说明配置成功





# 辅DNS服务器配置[Slave DNS Server]





## 安装bind




    
```bash
    yum install bind-*
```





## 配置文件





### 配置文件概览





需要创建的配置文件列表如下




    
```bash
    ls /var/named
```





输出




```sh
    localtime      
    named.rfc1912.zones
    bak-named.online.zones
    named.conf     
    rndc.conf
    bind.keys          
    named.online.zones
    zones
```





### 编辑主配置文件named.conf




    
```sh
    options {
            listen-on port 53 { 127.0.0.1;192.168.122.101; };
            listen-on-v6 port 53 { ::1; };
            directory       "/var/named";
            dump-file       "/var/named/data/cache_dump.db";
            statistics-file "/var/named/data/named_stats.txt";
            memstatistics-file "/var/named/data/named_mem_stats.txt";
            allow-transfer {
                    127.0.0.1;
                    192.168.122.101;
                    192.168.122.102;
            };
            allow-query     {
                    127.0.0.1/8;
                    192.168.122.0/24;
                    };
            recursion yes;
     
            dnssec-enable yes;
            dnssec-validation yes;
            dnssec-lookaside auto;
     
    /* Path to ISC DLV key */
    #       bindkeys-file "/etc/named.iscdlv.key";
     
    #       managed-keys-directory "/var/named/dynamic";
    };
     
     
    key "rndc-key" {
            algorithm hmac-md5;
            secret "fUgKXu0t1woSwFfVB8xOTQ==";
    };
     
    controls {
            inet 127.0.0.1 port 953
            allow { 127.0.0.1; } keys { "rndc-key"; };
    };
     
    #logging {
    #        channel default_debug {
    #                file "data/named.run";
    #                severity dynamic;
    #        };
    #};
     
    #zone "." IN {
    #       type hint;
    #       file "named.ca";
    #};
     
    #include "/etc/named.rfc1912.zones";
    #include "/etc/named.root.key";
    logging{
            channel simple_log {
                    file "/var/log/bind.log" versions 5 size 50m;
                    severity info;
                    print-time yes;
                    print-severity yes;
                    print-category yes;
            };
            category default{
                    simple_log;
            };
     
            channel queries_log {
                    file "/var/log/bind_queries.log" versions 5 size 50m;
                    print-time yes;
     
                    print-severity yes;
                    print-category yes;
            };
            category queries {
                    queries_log;
            };
            category lame-servers {
                    null;
            };
    };
     
    zone "." IN {
            type hint;
            file "named.ca";
    };
     
    include "etc/named.online.zones";
```





### 编辑named.online.zones配置文件




    
```sh
    zone "weittor-inc.com" {
            type slave;
            file "slaves/weittor-inc.com.zone";
            masters {
                    192.168.122.101;
           };
     
            allow-notify {
                    192.168.122.101;
            };
     
    };
     
     
     
     
     
    zone "168.192.in-addr.arpa." {
            type slave;     ##指定该服务器角色为辅DNS服务器
            file "slaves/168.192.in-addr.arpa.zone";
            masters {
                    192.168.122.101;    ##指定主DNS服务器地址
            };
     
            allow-notify {
                     192.168.122.101;
            };
    };
    
```




## 启动服务




    
```bash
    service named restart
    chkconfig named on //开机开启
```





查看辅DNS服务器是否同步成功




    
```bash
    ll var/named/slaves
```





# 客户端配置





设置client的DNS为刚刚搭建的DNS服务器




    
```bash
    cat /etc/resolv.conf
```





添加内容




    
```sh
    search weittor-inc.com
    nameserver 192.168.122.101
    nameserver 192.168.122.102
```





## 测试




    
```bash
    nslookup www.weittor-inc.com
    dig www.weittor-inc.com
```





至此，配置过程结束。





# 参考资料





[http://geekpeek.net/install-bind-centos/](http://geekpeek.net/install-bind-centos/)  

[http://www.centos.bz/2011/07/centos-install-bind-dns-server/](http://www.centos.bz/2011/07/centos-install-bind-dns-server/)





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



