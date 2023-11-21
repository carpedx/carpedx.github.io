---
layout: fragment
title: Docker安装SonarQube
tags: [docker]
description: 代码质量检查工具
keywords: docker
---



相关文章：[Docker安装GitLab](https://carpedx.com/fragment/docker-gitlab/)、[Docker安装Jenkins](https://carpedx.com/fragment/docker-jenkins/)

------



拉取postgres数据库

```shell
docker pull postgres
```



查看SonarQube镜像

```shell
docker search sonarqube
```



拉取GitLab镜像

```shell
docker pull sonarqube:9.9.3-community
```



准备 docker-compose

`/usr/local/docker/sonarqube_docker/docker-compose.yml`：

```yaml
version: '3.1'
services:
  db:
    image: 'postgres'
    container_name: db
    ports:
      - 5432:5432
    networks:
      - sonarnet
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      
  sonarqube:
    image: 'sonarqube:9.9.3-community'
    container_name: sonarqube
    depends_on:
      - db
    ports:
      - 9000:9000
    networks:
      - sonarnet
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar

networks:
  sonarnet:
    driver: bridge
```

创建容器

```shell
docker-compose up -d
```

跟踪日志

```shell
docker logs -f gitlab
```

修改中文显示：

<img src="/images/fragments/docker/docker-sonarqube_step3.webp" />




> **报错显示内存不足：**
>
> <img src="/images/fragments/docker/docker-sonarqube_step2.webp" />
>
> 增加虚拟内存：
>
> ```shell
> vim /etc/sysctl.conf
> ```
>
> ```shell
> vm.max_map_count=262144
> ```
>
> <img src="/images/fragments/docker/docker-sonarqube_step1.webp" />
>
> 修改生效：
>
> ```shell
> sysctl -p
> ```



测试访问：

http://192.168.31.102:9000/	默认账号admin，密码admin
