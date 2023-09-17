---
layout: post
title: 在Linux上使用Docker Swarm安装Portainer
categories: linux
description: 在Linux上使用Docker Swarm安装Portainer
keywords: linux, docker, swarm, portainer
---



**Portainer是一款Docker轻量级的图形化管理工具**

****



## Docker Swarm



>192.168.31.101 manager
>192.168.31.102 node1



#### 初始化

在 192.168.31.101 初始化 Swarm 集群

```shell
docker swarm init
```

<img src="/images/posts/linux/docker_swarm_portainer_step1.jpg"  />

查看加入Swarm的令牌

```shell
docker swarm join-token manager
```



#### 加入集群

在 192.168.31.102 服务器执行下面命令

```shell
docker swarm join --token SWMTKN-1-2xtsbqoj5p88aqe4l9ii4ry04mwx397cpdkirz8wacvzggevrw-ey5afzqnqspq4u145ttkqix6y 192.168.31.101:2377
```



#### 查看集群

在 192.168.31.101 下执行下面命令查看集群

```shell
docker node ls
```







## Portainer



#### 创建 Swarm 作用域的网络

```shell
docker network create --driver overlay carpedx-network
```



#### 创建部署文件

`/root/docker-portainer/docker-stack.yml` 内容如下：

```shell
version: '3.2'

services:
  agent:
    image: portainer/agent:2.5.1
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
    networks:
      - carpedx-network
    deploy:
      mode: global
      placement:
        constraints: [node.platform.os == linux]

  portainer:
    image: portainer/portainer-ce:2.5.1
    command: -H tcp://tasks.agent:9001 --tlsskipverify
    ports:
      - "9000:9000"
      - "8000:8000"
    volumes:
      - portainer_data:/data
    logging:
      options:
        max-size: 50m
    networks:
      - carpedx-network
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]

networks:
  carpedx-network:
    external: true

volumes:
  portainer_data:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.31.100,vers=4,soft,timeo=180,bg,tcp,rw"
      device: "192.168.31.100:/data/nfs/storage/portainer_data"
```

>192.168.31.100 使用了[NFS共享文件](https://carpedx.com/2023/09/17/linux_nfs/)，将配置文件共享到 192.168.31.100 服务器的 /data/nfs/storage/portainer_data 目录下



#### 部署堆栈

```shell
docker stack deploy -c docker-stack.yml mon
```

> `docker stack ls` 列出所有已部署的堆栈
>
> `docker stack rm <service_name>` 删除堆栈



#### 测试

访问 http://192.168.31.101:9000/，显示如下则正常

<img src="/images/posts/linux/docker_swarm_portainer_step2.jpg"  />



> 如果一直不能访问可以尝试重启 Docker：
>
> `systemctl restart docker`
