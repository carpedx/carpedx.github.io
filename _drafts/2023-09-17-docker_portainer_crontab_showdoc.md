---
layout: post
title: 使用Docker图形化管理工具Portainer创建crontab定时任务和showdoc API文档工具
categories: linux
description: 使用Docker图形化管理工具Portainer创建crontab定时任务和showdoc API文档工具
keywords: linux, docker, portainer, crontab, showdoc
---



**使用 `Docker` 图形化管理工具 `Portainer` 创建 `crontab` 定时任务和 `showdoc `API文档工具**

****



>相关链接：
>
>[Linux安装NFS实现文件共享](https://carpedx.com/2023/09/16/linux_nfs/)
>
>[在Linux上使用Docker Swarm安装Portainer](https://carpedx.com/2023/09/17/docker_swarm_portainer/)



#### Create stack：

<img src="/images/posts/linux/docker_portainer_crontab_showdoc_step1.jpg" />

<img src="/images/posts/linux/docker_portainer_crontab_showdoc_step2.jpg" />

Web editor：

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
    image: registry.cn-shanghai.aliyuncs.com/wushidu/php:7.4-fpm
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

  php-workerman-register:
    image: registry.cn-shanghai.aliyuncs.com/wushidu/php:7.4-fpm
    command: supervisord -n
    ports:
      - "1237:1237"
    volumes:
      - www_dir:/var/www
      - supervisor_config_pwr:/etc/supervisor/conf.d
      - php_config:/usr/local/etc/php
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
      replicas: 1

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
      
  supervisor_config_pwr:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/conf/supervisord/php-workerman-register"
      
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

