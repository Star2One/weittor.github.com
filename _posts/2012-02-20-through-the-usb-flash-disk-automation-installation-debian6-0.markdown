---
author: chris.hill
comments: true
date: 2012-02-20
excerpt: 详细的为你介绍了如何通过U盘自动化安装Debian6.0系统，使用工具：preseed
layout: post
slug: through-the-usb-flash-disk-automation-installation-debian6-0
title: 通过u盘自动化安装Debian6.0
categories:
- IT运维
tags:
- Linux
---

* * *





# 概述





根据Debian官方介绍，U盘安装有很多种方法，既有简单易用的，又有复杂但不失灵活的。  

由于chris.hill本人在实际项目没有配置网络，并且需要自动化安装大部分开发用的软件，故通过U盘安装时拷贝一个完整的系统ISO镜像进行安装。  

因此才有了下面这篇手册的诞生。





# u盘分区





## 使用 cfdisk 分区工具





创建一个 FAT16 的分区,注意只需要使用 U 盘的第一个分区即可。  

    提示：

        chris.hill的U盘在自己的ubuntu系统上面显示的设备名称为/dev/sdb，请根据自己的实际情况而定。  

执行命令：

```sh
cfdisk /dev/sdb
```




## 使用 mkdosfs 来创建 FAT16 文件系统





_可能需要先行安装 dosfstools 软件包_



```sh
mkdosfs /dev/sdb1
```


# 将 syslinux 安装到 FAT16 分区





为了在 U 盘引导后启动内核,我们要在 U 盘上放入一个 boot loader。  

_尽管任何 boot loader(比如 lilo)都应该可以胜任,不过还是使用 syslinux 更方便。_  

主要原因是它可以使用 FAT16 分区,而且只需要编辑一个文本文件就能对其进行配置。


    任何支持 FAT 文件系统的操作系统都可以用来改变 boot loader 的配置文件。





通过 syslinux 来引导系统(如果你的系统没有 syslinux 和 mtools 包,要先行安装)


```sh
syslinux /dev/sdb1
```


<!-- more -->



# 添加安装程序映像到 U 盘





挂载分区



```sh
mount /dev/sdb1 /mnt
```



复制安装程序映像文件到U盘


  * vmlinuz 或 linux (内核二进制文件)    

  * initrd.gz (初始化 ramdisk 映象)





可以选择使用安装程序的文本或者图形界面。后者放在 gtk 子目录下面。  

    如果需要改文件名，请注意 syslinux 只能处理 DOS(8.3)文件名。





## 下载安装镜像





下载地址:
<http://d-i.debian.org/daily-images/amd64/daily/hd-media/>





之后，拷贝镜像文件到 U 盘的根目录。





    提示：服务器一般都下载amd64版本的。




# 创建一个 syslinux.cfg 配置文件





接下来,在根目录下面创建一个 syslinux.cfg 配置文件,里面最少要包含下面两行(如果您使用 netboot 映象,修改内核文件名为“linux”):




```sh    
default vmlinuz append initrd=initrd.gz
```





对于图形安装程序,您需要添加 vga=788 到第二行尾部。





复制 debian6.0 完整 iso 镜像文件到 U 盘,即当前挂载的/mnt 目录下面。



```sh
cp debian-6.0.2-amd64-DVD-1.iso /mnt
```




如果不能从 U 盘启动,可能 MBR 不正确, 用下面这个命令修复它。



```sh
install-mbr /dev/sdb
```


    提示:该工具在软件包 mbr 里面,若没有此命令,执行命令 apt-get install mbr 进行安装。




# 编辑 syslinux.cfg 配置文件





添加自动化安装配置文件路径



```sh
vi syslinux.cfg 
```


内容如下：
    
```    
    default vmlinuz append initrd=initrd.gz vga=788 auto=true priority=critical console-keymaps-at/keymap=us debian-installer/locale=zh_CN DEBCONF_PRIORITY=critical preseed/file=/hd-media/preseed.cfg -- quiet prompt 0 timeout 0
```    





该文件中选项 preseed/file=/hd-media/preseed.cfg 用来指定从 U 盘里面获取preseed.cfg 配置文件。





# 编辑 preseed.cfg 配置文件


``` 
    d-i debian-installer/locale string zh_CN
    d-i debian-installer/language string zh_CN
    d-i debian-installer/country string CN
    d-i debian-installer/locale string zh_CN.UTF-8
    d-i localechooser/supported-locales en_US.UTF-8, zh_CN.GB18030,zh_CN.GB2312
      
    d-i console-keymaps-at/keymap select us
      
    linx-serialnumber       linx-serialnumber/preseed       boolean  true
    d-i  linx-serialnumber/number        string  rocky
      
    d-i netcfg/enable boolean false
    d-i netcfg/choose_interface select eth0
      
    d-i netcfg/get_nameservers string 192.168.1.1
    d-i netcfg/get_ipaddress string 192.168.1.101
    d-i netcfg/get_netmask string 255.255.255.0
    d-i netcfg/get_gateway string 192.168.1.1
    d-i netcfg/confirm_static boolean true
      
    d-i netcfg/get_hostname seen
    d-i netcfg/get_domain seen
      
    d-i netcfg/wireless_wep string
      
    d-i mirror/country string manual
    d-i mirror/http/hostname string 192.168.1.197
    d-i mirror/http/directory string /rocky
    d-i mirror/http/proxy string
      
    d-i passwd/root-login boolean true
    d-i passwd/make-user boolean false
    d-i passwd/root-password password rocky
    d-i passwd/root-password-again password rocky
      
    d-i passwd/make-user boolean true
    d-i passwd/user-fullname string d5000
    d-i passwd/username string d5000
    d-i passwd/user-password password d5000
    d-i passwd/user-password-again password d5000
    d-i passwd/user-default-groups string audio cdrom video d5000
      
    d-i clock-setup/utc boolean false
      
    d-i time/zone string Asia/Shanghai
      
    d-i clock-setup/ntp boolean false
      
    d-i partman-auto/disk string /dev/sda
    d-i partman-auto/method string regular
      
    d-i partman-lvm/device_remove_lvm boolean true
    d-i partman-md/device_remove_md boolean true
    #d-i partman-lvm/confirm boolean true
      
    #d-i partman-auto/choose_recipe select home
    #/dev/sda1             6.5G  1.3G  4.9G  21% /
    #tmpfs                1008M     0 1008M   0% /lib/init/rw
    #udev                   10M  652K  9.4M   7% /dev
    #tmpfs                1008M     0 1008M   0% /dev/shm
    #/dev/sda6             447G  199M  424G   1% /home
    #rd213:~# fdisk  -l
      
    #Disk /dev/sda: 500.1 GB, 500107862016 bytes
    #255 heads, 63 sectors/track, 60801 cylinders
    #Units = cylinders of 16065 * 512 = 8225280 bytes
    #Disk identifier: 0x000f23cd
      
    #   Device Boot      Start         End      Blocks   Id  System
    #/dev/sda1   *           1         851     6835626   83  Linux
    #/dev/sda2             852       60801   481548375    5  Extended
    #/dev/sda5             852        1603     6040408+  82  Linux swap / Solaris
    #/dev/sda6            1604       60801   475507903+  83  Linux
      
    d-i partman-auto/expert_recipe string           \
            boot-root ::                            \
                    128 512 256 ext3                \
                            $primary{ }             \
                            $bootable{ }            \
                            method{ format }        \
                            format{ }               \
                            use_filesystem{ }       \
                            filesystem{ ext3 }      \
                            mountpoint{ /boot }     \
                    .                               \
                    500 4000 5000 ext3              \
                            $primary{ }             \
                            method{ format }        \
                            format{ }               \
                            use_filesystem{ }       \
                            filesystem{ ext3 }      \
                            mountpoint{ / }         \
                    .                               \
                    96 512 300% linux-swap          \
                            method{ swap }          \
                            format{ }               \
                    .                               \
                    100 10000 -1 ext3               \
                      method{ format }        \
                            format{ }               \
                            use_filesystem{ }       \
                            filesystem{ ext3 }      \
                            mountpoint{ /home }     \
                    .
      
    #文件系统              容量  已用 可用 已用% 挂载点
    #/dev/sda2             6.5G  1.3G  4.9G  20% /
    #tmpfs                1008M     0 1008M   0% /lib/init/rw
    #udev                   10M  664K  9.4M   7% /dev
    #tmpfs                1008M     0 1008M   0% /dev/shm
    #/dev/sda1             228M   18M  199M   8% /boot
    #/dev/sda6             447G  199M  424G   1% /home
    #rd213:~# fdisk  -l
      
    #Disk /dev/sda: 500.1 GB, 500107862016 bytes
    #255 heads, 63 sectors/track, 60801 cylinders
    #Units = cylinders of 16065 * 512 = 8225280 bytes
    #Disk identifier: 0x000f23cd
      
    #   Device Boot      Start         End      Blocks   Id  System
    #/dev/sda1               1          31      248976   83  Linux
    #/dev/sda2   *          32         882     6835657+  83  Linux
    #/dev/sda3             883       60801   481299367+   5  Extended
    #/dev/sda5             883        1634     6040408+  82  Linux swap / Solaris
    #/dev/sda6            1635       60801   475258896   83  Linux
      
    #d-i partman-basicfilesystems/swap_check_failed boolean false
    #d-i partman-basicfilesystems/no_swap boolean false
    #d-i partman/confirm_write_new_label boolean true
    #d-i partman/choose_partition select finish
    #d-i partman/confirm boolean true
      
    d-i partman-partitioning/confirm_write_new_label boolean true
    d-i partman/choose_partition select finish
    d-i partman/confirm boolean true
    d-i partman/confirm_nooverwrite boolean true
      
    d-i partman/mount_style select uuid
      
    d-i apt-setup/non-free boolean false
    d-i apt-setup/contrib boolean false
      
    #tasksel        tasksel/first   multiselect linx
    tasksel tasksel/first  multiselect standard,web-server
    tasksel tasksel/desktop multiselect gnome,kde
    d-i      pkgsel/include string x-window-system-core gnome openssh-server build-essential tcsh ia32-libs ia32-libs-dev libc6-dev-i386 vim gcc g++ ttf-arphic-uming xfonts-intl-chinese
    d-i        pkgsel/upgrade select none
      
    #d-i finish-install/keepconsoles boolean true
      
    #d-i console-setup/charmap47  UTF-8
      
    #d-i grub-installer/only_rocky boolean true
    grub-installer  grub-installer/multipath        boolean true
    grub-installer  grub-installer/sataraid boolean true
    grub-installer  grub-installer/multipath-error  error
    grub-installer  grub-installer/bootdev  string
    grub-installer  grub-installer/with_other_os    boolean true
    grub-installer  grub-installer/only_debian      boolean true
    grub-installer  grub-installer/grub_not_mature_on_this_platform boolean false
      
    #d-i grub-installer/with_other_os boolean true
      
    d-i finish-install/reboot_in_progress note
    d-i debian-installer/exit/poweroff  boolean true
    
```



    提示:由于实际实施环境中,网络配置的不确定性,故我们选择在自动化安装的时候,通过指定选项“d-i netcfg/enable boolean false”跳过了网络配置这一步骤。



# 设置 BIOS 从 U 盘启动





将 u 盘与主机相连,调整 BIOS 里面的系统引导选项,指定系统从 U 盘启动之后,即可进行自动化安装配置。





* * *





作者：chris hill





微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)



