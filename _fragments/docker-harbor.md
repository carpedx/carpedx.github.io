---
layout: fragment
title: Docker安装Harbor
tags: [docker]
description: 私有镜像仓库,用于制作自定义镜像
keywords: docker
---



相关文章：[Docker安装GitLab](https://carpedx.com/fragment/docker-gitlab/)、[Docker安装Jenkins](https://carpedx.com/fragment/docker-jenkins/)、[Docker安装SonarQube](https://carpedx.com/fragment/docker-sonarqube/)

------



#### 安装 Harbor



1）下载 [Harbor](https://github.com/goharbor/harbor)



2）解压 Harbor

```shell
tar -zxvf harbor-offline-installer-v2.3.4.tgz -C /usr/local
```



3）进入目录，复制配置文件

```shell
cd /usr/local/harbor
cp harbor.yml.tmpl harbor.yml
```



4）修改 hostname，把 https 证书注释

<img src="/images/fragments/docker/docker-harbor_step1.webp" />



5）执行，依赖于 docker、docker-compose

```shell
./install.sh
```



6）执行成功，测试访问 http://192.168.31.102:80/

默认账号：admin

默认密码：Harbor12345



7）添加个新的镜像仓库

<img src="/images/fragments/docker/docker-harbor_step2.webp" />



#### Docker 推送 Harbor

1）配置 docker/daemon.json 文件：

```shell
vim /etc/docker/daemon.json
```

`/etc/docker/daemon.json` 内容如下：

```shell
{
  "insecure-registries": ["192.168.31.102:80"]
}
```



2）重启 docker

```shell
systemctl restart docker
```



3）设置 Docker Harbor 账号密码

```shell
docker login -u admin -p Harbor12345 192.168.31.102:80
```



4）镜像改名

```shell
docker tag 8f00759f5b3f 192.168.31.102:80/repo/mytest:v1.0.0
```



5）推送

```shell
docker push 192.168.31.102:80/repo/mytest:v1.0.0
```

推送成功，测试访问 http://192.168.31.102:80/

<img src="/images/fragments/docker/docker-harbor_step3.webp" />



6）拉取

```shell
docker pull 192.168.31.102:80/repo/mytest:v1.0.0
```





#### Jenkins 整合 Harbor



**让 Jenkins 能使用 Docker 命令** 

1）在宿主机修改 docker.sock 的权限

```shell
cd /var/run
chown root:root docker.sock
chmod o+rw docker.sock
```



3）Jenkins 新增数据卷

```shell
vim /usr/local/docker/jenkins_docker/docker-compose.yml
```

`/usr/local/docker/jenkins_docker/docker-compose.yml`

```yaml
- '/usr/bin/docker:/usr/bin/docker'
- '/etc/docker/daemon.json:/etc/docker/daemon.json'
```

<img src="/images/fragments/docker/docker-harbor_step4.webp" />



4）重新构建 Jenkins 

```shell
docker-compose up -d
```



5）进入容器测试 Docker 命令

```shell
docker exec -it jenkins bash
docker version
```



**让 Jenkins 制作自定义镜像并推送 Harbor** 

1）删除 mytest 项目下 `docker-compose.yml`



2）提交推送后在 GitLab 给项目打标签 v3.0.0

<img src="/images/fragments/docker/docker-harbor_step5.webp" />



3）在 Jenkins 项目配置中删除之前的构建操作

<img src="/images/fragments/docker/docker-harbor_step6.webp" />



4）添加新的构建操作

```shell
mv target/*.jar docker/
docker build -t mytest:$tag docker/
docker login -u admin -p Harbor12345 192.168.31.102:80
docker tag mytest:$tag 192.168.31.102:80/repo/mytest:$tag
docker push 192.168.31.102:80/repo/mytest:$tag
```

<img src="/images/fragments/docker/docker-harbor_step7.webp" />

<img src="/images/fragments/docker/docker-harbor_step8.webp" />



5）登录 Jenkins 重新构建

<img src="/images/fragments/docker/docker-harbor_step9.webp" />



6）登录 Harbor 查看是否构建成功

<img src="/images/fragments/docker/docker-harbor_step10.webp" />



**Jenkins 通知目标服务器运行 Docker 镜像**

1）创建脚本文件：

`/root/deploy.sh`

```shell
harbor_addr=$1
harbor_repo=$2
project=$3
version=$4
container_port=$5
host_port=$6

imageName=$harbor_addr/$harbor_repo/$project:$version

echo $imageName

containerId=`docker ps -a | grep ${project} | awk '{print $1}'`

echo $containerId

if [ "$containerId" != "" ] ; then
  docker stop $containerId
  docker rm -f $containerId
fi

tag=`docker images | grep ${project} | awk '{print $2}'`

echo $tag
echo $imageName

if [[ "$tag" =~ "$version" ]] ; then
  docker rmi -f $imageName
fi

docker login -u admin -p Harbor12345 $harbor_addr

docker pull $imageName

docker run -d -p $host_port:$container_port --name $project $imageName

echo "SUCCESS"
```



2）修改执行权限

```shell
chmod a+x deploy.sh
```



3）加入环境变量

```
echo $PATH
mv deploy.sh /usr/bin/
```



4）Jenkins 增加字符参数

<img src="/images/fragments/docker/docker-harbor_step11.webp" />

<img src="/images/fragments/docker/docker-harbor_step12.webp" />



5）Jenkins 增加构建后操作

<img src="/images/fragments/docker/docker-harbor_step13.webp" />

<img src="/images/fragments/docker/docker-harbor_step14.webp" />



6）执行构建，构建成功访问 http://192.168.31.102:8081/test

<img src="/images/fragments/docker/docker-harbor_step15.webp" />

