---
author: chris.hill
comments: true
date: 2012-02-20
excerpt: 判断局域网内存活主机的简单shell脚本
layout: post
slug: shell-script-ping-node-sh
title: 判断局域网内存活主机的简单shell脚本ping-node.sh
categories:
- 脚本工具
tags:
- Shell
---

* * *





分享一个自己写的判断局域网内存活主机的脚本。
_说明_：主要功能依赖于网络工具ping。

<!-- more -->


具体参见脚本

```sh
    #!/bin/bash
     
    ###############################################################################
    # Name: ping-node.sh
    # Author: chris.hill
    # Version: 1.0
    # Reversion: 2012-2-20
    # Description: 判断局域网内主机是否存活，可以指定ip地址或者指定包含ip地址的文件
    # Depend on: ip地址格式-192.168.100.11
    # 包含ip地址的文件格式-
    # 192.168.100.11
    # 192.168.100.12
    # Notes:
    # Usages: ./ping-node.sh
    ###############################################################################
     
    #TODO: Add debug log
     
    isalive()
    {
     NODE=$1
            ping -c 1 $NODE >/dev/null 2>&1
          if [ $? -eq 0 ]
            then
               echo "$NODE is alive..."
           else
               echo "$NODE is unreachable..."
     fi
     
    }
     
    isalive_hosts ()
    {
          if [ ! -d "./report" ]
     then
               mkdir report
       else
               for i in `cat $1 |grep -v '^#'|awk '{print $1}'`
                   do
                 isalive "$i"
               done >>report/$DATE.txt 2>&1
          fi
     
        if [ "$?" -eq "0" ]
        then
               echo "The ping test is done..."
                    cat report/$DATE.txt
       fi
    }
    main_loop ()
    {
        echo -e -n "\tHit the return key to continue..."
        read DUMMY
    }
    DATE=`date +%H:%M_%m-%d-%Y`
    USER_NAME=`whoami`
    HOST_NAME=`hostname`
    main ()
    {
    cat <<EOF
            +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
      User:$USER_NAME Host:$HOST_NAME Date:$DATE
         +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
                      1 ) Insert the ip what you want to test
                            2 ) Insert the hosts file what filled with the hosts'ip
                            q ) quit
    EOF
     
    }
     
    while [ 1 -gt 0 ]
    do
      tput clear
         main
       echo -e -n "\tYour Choice is [ 1-2 or q ]>>"
            read CHOICE
        case $CHOICE in
            1 )
                echo -e "Insert your testing ip: \n"
               read IP_ADDR
               isalive $IP_ADDR
                   echo -e "\n"
               main_loop
                  ;;
         2 )
                echo -e "Insert your hosts file: \n"
               read IP_HOSTS
              isalive_hosts $IP_HOSTS
                    echo -e "\n"
               main_loop
                  ;;
         q|Q )
              echo "bye..."
              exit
               ;;
         * )
                echo "Unknown choice ..."
                 echo "Usage: 1\2\q "
               main_loop
          esac
     
    done
    

```




* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



