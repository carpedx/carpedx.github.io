---
layout: post
title: 使用Portainer搭建Nginx+PHP环境
categories: docker
description: 使用Portainer搭建Nginx+PHP环境
keywords: linux, docker, portainer, nginx, php, openresty
---



**使用 `Docker` 图形化管理工具 `Portainer`搭建 `Nginx + PHP` 环境**

****



>相关链接：
>
>[Linux安装NFS实现文件共享](https://carpedx.com/2023/09/16/linux_nfs/)
>
>[在Linux上使用Docker Swarm安装Portainer](https://carpedx.com/2023/09/17/docker_swarm_portainer/)



## 搭建

<img src="/images/posts/docker/portainer_nginx_php_step1.webp" />

<img src="/images/posts/docker/portainer_nginx_php_step2.webp" />

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
      replicas: 2

  php:
    image: registry.cn-hangzhou.aliyuncs.com/tanwb/php:7.4-fpm
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
      replicas: 2

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
> `Dockerfile` 增加了：
>
> ```yaml
># 安装包管理器 Supervisor
> apt-get install -y --no-install-recommends supervisor
> # 安装定时任务 Cron
> apt-get install -y cron
> ```
> 
> > 如果你要根据此 `Dockerfile` 创建自己的镜像请注意，可能会在执行 `docker build -t my-image .` 时报网络连接异常，此时可能需要设置 `docker dns` ：
>>
> > `daemon.json` 中增加：`"dns":["8.8.8.8","8.8.4.4"]`



创建成功如下：

<img src="/images/posts/docker/portainer_nginx_php_step3.webp" />

测试访问 `http://192.168.31.101/` 网页如下：

<img src="/images/posts/docker/portainer_nginx_php_step4.webp" />



## 配置



在 `/data/nfs/www` 创建 index.php 测试文件，内容为：

```php
<?php

phpinfo();

echo '<pre>';
print_r($_SERVER);
echo '</pre>';
```



修改 `/data/nfs/conf/nginx/conf.d/default.conf ` 文件内容为：

```tex
server {
  listen		80;
  server_name		localhost;

  set $root_path	/var/www;

  root			$root_path;
  index			index.php index.htm index.html;

  location / {
    if (!-e $request_filename) {
      rewrite  ^(.*)$  /index.php?s=/$1  last;
    }
  }

  location ~ \.php$ {
    try_files $uri = 404;
    fastcgi_pass   php:9000;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
  }
}
```



在 `/data/nfs/conf/supervisord` 目录下创建 `supervisord.conf` 文件：

```tex
[program:php]
command = docker-php-entrypoint php-fpm
loglevel=warn
stdout_logfile=none
stderr_logfile=none
```



重启 Docker Nginx 和 PHP：

<img src="/images/posts/docker/portainer_nginx_php_step5.webp" />



再次测试访问 `http://192.168.31.101/` 应该如下：

<img src="/images/posts/docker/portainer_nginx_php_step6.webp" />
