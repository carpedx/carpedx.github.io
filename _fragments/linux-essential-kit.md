---
layout: fragment
title: Linux系统必备工具
tags: [linux]
description: Linux系统必备工具
keywords: linux
---



相关文章：[Linux系统IP和主机名配置](https://carpedx.com/fragment/linux-ip-and-hostname-config/)



#### 包

下载并安装系统中所有可用的更新，包括安全更新、bug 修复和新功能：

```shell
yum -y update
```



安装 epel-release 包管理器

```shell
yum install -y epel-release
```



工具包合集，包含 ifconfig 等命令

```shell
yum install -y net-tools
```



#### Vim

安装 vim 编辑器

```shell
yum install -y vim
```



#### 防火墙

关闭防火墙，关闭防火墙开机自启

```shell
systemctl stop firewalld
systemctl disable firewalld.service
```



#### 用户


创建 test 用户，并修改 test 用户密码

```shell
useradd test
passwd 123456
```




配置 test 用户具有 root 权限，方便后期加 sudo 执行 root 权限的命令

```shell
vim /etc/sudoers
```

在%wheel这行下面添加一行：

```shell
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL

test ALL=(ALL)       NOPASSWD:ALL
```



#### 虚拟机

卸载虚拟机自带的JDK

```shell
rpm -qa | grep -i java | xargs -n1 rpm -e --nodeps
```

> rpm -qa：查询所安装的所有rpm软件包
>
> grep -i：忽略大小写
>
> xargs -n1：表示每次只传递一个参数
>
> rpm -e --nodeps：强制卸载软件



#### 重启

```shell
reboot
```

