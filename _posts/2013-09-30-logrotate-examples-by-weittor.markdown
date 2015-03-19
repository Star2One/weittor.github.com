---
author: chris.hill
comments: true
date: 2013-09-30
excerpt: Logrotate日志轮询服务使用说明
layout: post
slug: logrotate-examples-by-weittor
title: Logrotate日志轮询服务使用说明
categories:
- IT运维
tags:
- Linux
---

* * *





有效管理log日志是系统管理员必备的技能之一。  

_chris hill_在最近的工作中，接触到了logrotate有关的内容，现总结一些常用的功能，分享给大家。  

本文主要介绍如何通过logrotate工具实现如下操作:







  * 当日志超过一定大小之后，对日志进行轮询


  * 日志轮询之后，继续向新的日志文件输出日志


  * 压缩轮询后的日志


  * 指定轮询时的压缩选项


  * 为轮询的日志指定特定的名称


  * 日志轮询之后，立即执行自定义的shell脚本


  * 删除过期的轮询日志文件


  * 通过邮件发送轮询的日志信息





# Logrotate简介





logrotate程序是一个日志文件管理工具。  

通过logrotate工具，可以自动进行日志转储、删除、压缩和邮件发送的工作。  

我们可以根据日志文件的大小，也可以根据其天数来转储，这个过程一般通过 cron 程序来执行。 
logrotate 程序还可以用于压缩日志文件，以及发送日志到指定的E-mail 。





logrotate 的运行分为三步:







  * 判断系统的日志文件


  * 建立转储计划以及参数


  * 通过cron daemon 服务(*下面的代码使用了 Red Hat Linux 缺省的crontab服务 *)来执行logrotate



<!-- more -->



# 配置选项说明





## Logrotate配置文件





为了保证Logrotate配置正确，需要了解各个配置文件的用处。







  * /usr/sbin/logrotate – logrotate执行程序


  * /etc/cron.daily/logrotate – 需要每天执行的logrotate脚本




    
    
```sh
cat /etc/cron.daily/logrotate
```
    





代码如下




    
```sh
#!/bin/sh
/usr/sbin/logrotate /etc/logrotate.conf
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```







  * /etc/logrotate.conf – logrotate程序的主配置文件




    
    
```sh
cat /etc/logrotate.conf
```



    
内容如下：

```    
    weekly
    rotate 4
    create
    include /etc/logrotate.d
    /var/log/wtmp {
        monthly
        minsize 1M
        create 0664 root utmp
        rotate 1
    }
``` 







  * /etc/logrotate.d – 可单独放置不同程序的日志轮询配置文件或者自定义的日志轮询配置文件在该目录下，便于管理。
许多程序（典型的应用有：apache, samba, cron以及syslog）都将程序轮询的配置文件放置于该目录下面。  

例如yum的log轮询文件如下:




    
    
```sh
cat /etc/logrotate.d/yum
```



内容如下：
    
```    
    /var/log/yum.log {
        missingok
        notifempty
        size 30k
        yearly
        create 0600 root root
    }
```    





## Logrotate指定文件大小





设置每当log文件增加到1M时，就进行轮询(例如：_chris hill_自定义的日志文件为/tmp/weittor.log)。




    
    
```sh
cat logrotate.conf
```


内容如下：
    
```    
    /tmp/weittor.log {
            size 1M
            create 700 weittor weittor
            rotate 4
    }
```




以上三个选项的解释如下:







  * **size 1M**  指定日志文件大于或等于1M时，即进行轮询。


  * **create 700 weittor weittor** 对原始log进行轮询，并创建新的日志文件，新文件的权限为700，属主和数组分别为weittor:weittor。


  * **rotate 4** 轮询文件保留的数量。这里仅保留4个经过轮询的日志文件。
>注意：当写入该日志文件的应用程序没有经过重启操作的情况下，应用程序会持续写入由该方式所产生的轮询日志文件，即使该轮询日志文件已经经过了重命名(weittor.log.1)。





## copytruncate选项





解释：持续写入日志文件到轮询之后新创建的文件。




    
    
```sh
cat logrotate.conf
```


内容如下：
    
```    
    /tmp/weittor.log {
             size 1m
             copytruncate
             rotate 4
    }
```    





copytruncate选项告诉logrotate通过复制的方式创建轮询的日志文件，之后将原日志文件大小清零。  

这种方式对持续写入该日志文件的程序不会造成影响，能够保证生成该日志的程序能够一直将日志输入到正常的日志文件内。  

与copytruncate选项作用相对的参数nocopytruncate，表示备份日志文件但是不截断，即只备份日志文件，但是不清空该文件。





## Logrotate compress选项





解释：压缩日志。  

使用compress选型，可以通过gzip工具对轮询的日志进行压缩处理。




    
    
```sh
cat logrotate.conf
```



内容如下：    
    
```
    /tmp/weittor.log {
            size 1k
            copytruncate
            create 700 bala bala
            rotate 4
            compress
    }
```    





输出的压缩日志格式为




    
    
```sh
ls /tmp/weittor*
```



输出内容如下：    

```
weittor.log.1.gz weittor.log
```    





## Logrotate dateext选项





解释：配置带有日期格式的轮询日志文件




    
    
```sh
cat logrotate.conf
```



输出内容如下：    

```    
    /tmp/weittor.log {
            size 1M
            copytruncate
            create 700 weittor weittor
            dateext
    dateformat -%Y%m%d
            rotate 4
            compress
    }
```    





日志输出格式如下




    
    
```sh
ls -lrt /var/log/messages*
```


输出内容如下：

    
```    
    -rw------- 1 root root 963 Sep 22 03:28 /var/log/messages
    -rw------- 1 root root 239 Sep 26 09:52 /var/log/messages-20130926.gz
```    





_注意_







  * dateformat必须配合dateext使用，目前暂只支持 %Y %m %d %s 这四个参数


  * 如上的日志轮询配置每天仅执行一次。如果logrotate在同一天执行相同的操作的话，前一次轮询所产生的文件会和后面轮询所产生的轮询日志文件造成文件名冲突。因此，当天logrotate执行成功一次之后，后续的轮询将不会执行。





## Logrotate monthly/daily/weekly 选项





解释：每月/每天/每周执行轮询操作







  * 每月执行轮询操作




    
    
```sh
cat logrotate.conf
```



输出内容如下：    

```    
    /tmp/weittor.log {
            monthly
            copytruncate
            rotate 4
            compress
    }
```    







  * 每周执行轮询操作




    
    
```sh
cat logrotate.conf
```



输出内容如下：    

```    
    /tmp/weittor.log {
            weekly
            copytruncate
            rotate 4
            compress
    }
```    







  * 每天执行轮询操作




    
    
```sh
cat logrotate.conf
```


输出内容如下：
    
```    
    /tmp/weittor.log {
            daily
            copytruncate
            rotate 4
            compress
    }
```    





## Logrotate postrotate endscript选项





解释：在轮询日志操作执行之后，立即执行自定义的shell脚本。  

Logrotate允许用户在日志轮询操作执行之后，运行自定义的shell脚本。  

如下配置指定在轮询操作之后，执行自定义的myscript.sh。




    
    
```sh
cat logrotate.conf
```


输出内容如下：
    
```    
    /tmp/weittor.log {
            size 1m
            copytruncate
            rotate 4
            compress
            postrotate
                   /home/weittor/myscript.sh
            endscript
    }
```    





## Logrotate postrotate endscript 选项





解释：在轮询日志操作执行之前执行自定义的shell脚本




    
    
```sh
cat logrotate.d/messages
```



输出内容如下：    

```    
    /var/log/messages{
    prerotate
    /usr/bin/chattr -a /var/log/messages
    endscript
    postrotate
    /usr/bin/kill -HUP syslogd
    /usr/bin/chattr +a /var/log/messages
    endscript
    }
```    







  * 第一行指定脚本对 /var/log messages 有效。


  * prerotate 命令指定转储以前的动作



    * /usr/bin/chattr -a 去掉/var/log/messages文件的“只追加”属性 


    * endscript 结束 




  * prerotate 部分的脚本postrotate 指定转储后的动作。



    * /usr/bin/killall -HUP syslogd 用来重新初始化系统日志守护程序 syslogd。


    * /usr/bin/chattr +a /var/log/messages 重新为 /var/log/messages 文件指定“只追加”属性，这样防止程序员或用户覆盖此文件。




  * 最后的 endscript 用于结束 postrotate 部分的脚本。





## 9. Logrotate maxage选项





解释：删除过期的轮询日志文件。  

通过maxage选项，使logrotate在指定时间内删除过期的轮询日志文件。  

如下配置，指定logrotate删除10天之后的轮询日志文件。




    
    
```sh
cat logrotate.conf
```



输出内容如下：    

```    
    /tmp/weittor.log {
            size 1m
            copytruncate
            rotate 4
            compress
            maxage 10
    }
```    





## Logrotate missingok选项





解释：忽略轮询错误信息。  

通过missingok选项，能够忽略由于当前文件不存在所引起的报错信息。




    
    
```sh
cat logrotate.conf
```



输出内容如下：    
   
``` 
    /tmp/weittor.log {
            size 1k
            copytruncate
            rotate 4
            compress
            missingok
    }
```    





## Logrotate compresscmd and compressext选项





解释：自定义压缩指令。




    
    
```sh
cat logrotate.conf
```


输出内容如下：
    
```    
    /tmp/weittor.log {
            size 1m
            copytruncate
            compress
            compresscmd /bin/bzip2
            compressext .bz2
            rotate 4
    }
    
```




选项说明







  * compress 启用日志压缩功能


  * compresscmd /bin/bzip2 自定义压缩指令。例如：/bin/bzip2


  * compressext .bz2 轮询日志压缩文件的扩展名。默认扩展名为.gz，这里设置为：.bz2





## 与mail有关选项







  * errors address 轮询时的错误信息发送到指定的Email 地址


  * mail address 把轮询的日志文件发送到指定的Email 地址


  * nomail 轮询时不发送日志文件




    
    
```sh
cat logrotate.conf
```



输出内容如下：    

```    
    /tmp/weittor.log {
            size 1m
            copytruncate
    mail weittor#mail.com
            rotate 4
    }
```    





更多内容请访问[维特说](www.weittor.com)





# 参考资料





<http://blog.csdn.net/balderfan/article/details/7571773>

<http://linux.vbird.org/linux_basic/0570syslog.php#rotate>  

<http://man.he.net/man8/logrotate>  

<http://www.thegeekstuff.com/2010/07/logrotate-examples/>





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



