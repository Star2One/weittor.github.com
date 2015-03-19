---
author: chris.hill
comments: true
date: 2013-06-16
excerpt: 禁用kvm网络组件dnsmasq的DHCP功能
layout: post
slug: disable-dhcp-function-in-dnsmasq
title: 禁用kvm网络组件dnsmasq的DHCP功能
categories:
- 云计算与虚拟化
tags:
- KVM
---

* * *

#配置过程

查看原始网络配置

```sh
virsh net-edit default
```
原始文件

```
<network>
  <name>default</name>
  <uuid>721c0d9b-5fac-1af4-28a7-e5bf72a57efa</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0' />
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254' />
    </dhcp>
  </ip>
</network>
```
删除默认网络default

```sh
virsh net-destroy default
```
重新添加默认网络default

```sh
virsh net-edit default
```
去掉其中的DHCP选项

```
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254' />
    </dhcp>
```
新配置文件内容

```
<network>
  <name>default</name>
  <uuid>f014ff64-fcc9-cb50-78b9-284f821c0381</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0' />
  <mac address='52:54:00:16:A1:79'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
  </ip>
</network>
```
重启libvirt服务

```sh
/etc/init.d/libvirt-bin restart
```
#参考资料

<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Virtualization_Host_Configuration_and_Guest_Installation_Guide/chap-Virtualization_Host_Configuration_and_Guest_Installation_Guide-Libvirt_network_booting.html>

<https://help.ubuntu.com/community/KVM/Networking>

<http://www.krisbuytaert.be/blog/disabling-dhcp-libvirt-setup>







* * *


作者：chris hill



微博：[@翻滚的蜗牛blog](http://www.weibo.com/weittor)

