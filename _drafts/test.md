---
layout: post
title: Linux下搭建SVN服务器并实现提交自动更新
categories: linux
description: Linux下搭建SVN服务器并实现提交自动更新
keywords: linux, svn
---



**NFS 是 Network File System (网络文件系统)，主要是通过网络让不同的服务器之间可以共享文件或目录。可以通过挂载的方式让 NFS 服务器共享的目录挂载到 NFS 客户端本地目录下。**

------



> NFS 服务端：192.168.31.122
>
> NFS 客户端：192.168.31.221



## NFS



#### 安装NFS

查看 NFS 是否安装

```shell
rpm -qa | grep nfs
```

安装 NFS

```shell
yum install nfs-utils
```

<img src="/images/posts/linux/linux_nfs_step1.jpg"  />



#### 配置共享目录

创建 NFS 共享目录

```shell
mkdir /data/nfs
```

NFS 服务配置文件

```shell
vim /etc/exports
```

`exports` 文件：

```shell
# exports 常用参数
# ro 只读
# rw 读写
# root_squash 当NFS客户端以root管理员访问时，映射为NFS服务器的匿名用户
# no_root_squash 当NFS客户端以root管理员访问时，映射为NFS服务器的root管理员
# all_squash 无论NFS客户端使用什么账户访问，均映射为NFS服务器的匿名用户
# sync 同时将数据写入到内存与硬盘中，保证不丢失数据
# async 优先将数据保存到内存，然后再写入硬盘。这样效率更高，但可能会丢失数据
/data/nfs 192.168.31.0/24(rw,sync,no_root_squash)
```

> 192.168.31.0/24 表示可以容纳254个主机（从192.168.31.1到192.168.31.254）



#### 启动 NFS

启动 NFS 并设置开机自动启动

```shell
systemctl restart rpcbind && systemctl enable rpcbind
systemctl restart nfs-server && systemctl enable nfs-server
```

> rpcbind是用于NFS服务的，在NFS服务启动之前，必须先启动rpcbind服务。
>
> NFS 在文件传送过程中依赖于 RPC 协议



#### 显示挂载信息

```shell
showmount -e
```













## Docker



#### 更新YUM

```shell
yum -y update
```



#### 安装 containerd.io

```shell
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
```



#### 安装一些其他依赖

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```



#### 安装 Docker

```shell
yum install -y docker-ce 
```



#### 启动 Docker

```shell
# 设置开启启动
systemctl enable docker
# 启动
systemctl start docker
```











## Docker 安装 Portainer



首先，创建 Portainer Server 将用来存储其数据库的卷：

```shell
docker volume create mon_portainer_data
```



然后，下载并安装 Portainer Server 容器：

```shell
docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v mon_portainer_data:/data portainer/portainer-ce:latest
```



具体可参考官网 [Portainer文档](https://docs.portainer.io/start/install-ce/server/docker/linux)









### 客户端挂载 NFS

```shell
# 显示NFS服务器192.168.31.122上可供挂载的文件系统
showmount -e 192.168.31.122
# 挂载192.168.31.122服务器上/nfs目录到本地/nfs目录下
mount -t nfs 192.168.31.122:/nfs /nfs
```

https://blog.csdn.net/hdyebd/article/details/84547969
