---
layout: post
title: Portainer搭建Nginx+PHP环境
categories: docker
description: Portainer搭建Nginx+PHP环境
keywords: linux, docker, portainer, nginx, php, openresty
---



**使用 `Docker` 图形化管理工具 `Portainer`搭建 `Nginx + PHP` 环境**

****



>相关链接：
>
>[Linux安装NFS实现文件共享](https://carpedx.com/2023/09/16/linux_nfs/)
>
>[在Linux上使用Docker Swarm安装Portainer](https://carpedx.com/2023/09/17/docker_swarm_portainer/)



## 配置环境

#### Create stack：

<img src="/images/posts/linux/docker_portainer_crontab_showdoc_step1.jpg" />

<img src="/images/posts/linux/docker_portainer_crontab_showdoc_step2.jpg" />

`Editor` 内容如下：

```yaml
version: '3.2'

services:
  nginx:
    image: openresty/openresty
    environment:
      TZ: "Asia/ShangHai"
    ports:
      - "80:80"
    volumes:
      - nginx_config:/etc/nginx
      - www_dir:/var/www
    logging:
      options:
        max-size: 50m
    networks:
      - carpedx-network
    sysctls:
      - net.ipv4.vs.conn_reuse_mode=0
      - net.ipv4.vs.expire_nodest_conn=1
    deploy:
      mode: replicated
      replicas: 4

  php:
    image: registry.cn-hangzhou.aliyuncs.com/carpe/php:7.4-fpm
    command: supervisord -n
    ports:
      - "7000:9000"
      - "22001:22001"
      - "22002:22002"
    volumes:
      - www_dir:/var/www
      - supervisor_config:/etc/supervisor/conf.d
      - crontab_config:/etc/cron.d
      - php_config:/usr/local/etc/php
      - php_sessionpath:/sessionpath
    logging:
      options:
        max-size: 50m
    networks:
      - carpedx-network
    sysctls:
      - net.ipv4.vs.conn_reuse_mode=0
      - net.ipv4.vs.expire_nodest_conn=1
    deploy:
      mode: replicated
      replicas: 4
      
networks:
  carpedx-network:
    external: true
    
volumes:
  nginx_config:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/conf/nginx"

  www_dir:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/www"

  supervisor_config:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/conf/supervisord"
      
  crontab_config:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/conf/crontab"
      
  php_config:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/conf/php"

  php_sessionpath:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/storage/php_sessionpath"
```



> `registry.cn-hangzhou.aliyuncs.com/carpe/php:7.4-fpm` 的镜像内容是根据 [carpedx/docker-php](https://github.com/carpedx/docker-php/blob/main/7.4/fpm/bullseye/Dockerfile) 创建的。
>
> `php.ini` 增加了：`session.save_path = /sessionpath`
>
> `Dockerfile` 增加了：
>
> ```yaml
> # 安装包管理器 Supervisor
> apt-get install -y --no-install-recommends supervisor
> # 安装定时任务 Cron
> apt-get install -y cron
> ```
>
> > 如果你要根据此 `Dockerfile` 创建自己的镜像请注意，可能会在执行 `docker build -t my-image .` 时报网络连接异常，此时可能需要设置 `docker dns` ：
> >
> > `daemon.json` 中增加：`"dns":["8.8.8.8","8.8.4.4"]`



创建成功：

<img src="/images/posts/linux/docker_portainer_crontab_showdoc_step3.jpg" />



## 安装 showdoc



> 相关链接：[showdoc API](https://www.showdoc.com.cn/)



NFS服务器（192.168.31.100）上， `/data/nfs/www` 目录下 将 `showdoc` 放上（使用 `git clone` 或 `Xftp`）：

<img src="/images/posts/linux/docker_portainer_crontab_showdoc_step4.jpg" />

> 文件夹改个名，方便后续操作：`mv showdoc-3.2.2 showdoc`



192.168.31.101

192.168.31.102

安装PHP、nginx、php-fpm

/etc/hosts
