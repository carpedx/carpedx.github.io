---
layout: fragment
title: Linux系统IP和主机名配置
tags: [linux]
description: Linux系统IP和主机名配置
keywords: linux
---



#### 配置IP

**VMware配置**

编辑 > 虚拟网络编辑器

<img src="/images/fragments/linux/linux-ip-and-hostname-config_step1.jpg" />

<img src="/images/fragments/linux/linux-ip-and-hostname-config_step2.jpg" />



**Linux配置**

```she
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

ifcfg-ens33：

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static	# dhcp动态获取IP，static静态获取IP
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=b8f2a0d8-c5bc-46d5-891d-2e04dd9eb5ae
DEVICE=ens33
ONBOOT=yes

# IP地址
IPADDR=192.168.31.101
# 网关
GATEWAY=192.168.31.2
# 域名解析器
DNS1=192.168.31.2
```



#### 配置主机名

```shell
vi /etc/hostname
```

hostname：

```shell
centos101
```



#### 配置主机名称映射

```shell
vi /etc/hosts
```

hosts：

```shell
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.31.101  centos101
```



#### 全部配置完成后重启

```shell
reboot
```



接着可以安装 [Linux系统必备工具](https://carpedx.com/fragment/linux-essential-kit/)
