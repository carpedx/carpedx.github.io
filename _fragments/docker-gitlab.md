---
layout: fragment
title: Docker安装GitLab
tags: [docker]
description: 代码仓库管理系统
keywords: docker
---



查看GitLab镜像

```shell
docker search gitlab
```



拉取GitLab镜像

```shell
docker pull gitlab/gitlab-ce
```



准备 docker-compose

`/usr/local/docker/gitlab_docker/docker-compose.yml`：

```yaml
version: '3.1'
services:
  gitlab:
    image: 'gitlab/gitlab-ce'
    container_name: gitlab
    restart: always
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.31.101:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
      - '8929:8929'
      - '2224:2224'
    volumes:
      - './config:/etc/gitlab'
      - './logs:/var/log/gitlab'
      - './data:/var/opt/gitlab'
```

创建容器

```shell
docker-compose up -d
```

跟踪日志

```shell
docker logs -f gitlab
```



测试访问：

http://192.168.31.101:8929/	默认账号root



进入容器获取密码：

```shell
docker exec -it gitlab base
```

```shell
cat /etc/gitlab/initial_root_password
```

<img src="/images/fragments/docker/docker-gitlab_step1.webp" />

