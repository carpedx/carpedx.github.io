---
layout: fragment
title: Docker安装Jenkins
tags: [docker]
description: Docker安装Jenkins
keywords: docker
---


#### 安装 JDK

```shell
tar -zxvf jdk-8u181-linux-x64.tar.gz -C /usr/local/
```



#### 安装Maven

```shell
tar -zxvf apache-maven-3.9.5-bin.tar.gz -C /usr/local/
```



#### 给文件夹改个名字

```shell
cd /usr/local/
mv jdk1.8.0_181/ jdk
mv apache-maven-3.9.5/ maven
```



#### 配置Maven

```shell
vim /usr/local/maven/conf/setting.xml
```

`<mirrors></mirrors>` 标签加上：

```shell
<mirror>
	<id>nexus-aliyun</id>
	<mirrorOf>*</mirrorOf>
	<name>Nexus aliyun</name>
	<url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

`<profiles></profiles>` 标签加上：

```she
<profile>
    <id>jdk18</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```

`<settings></settings>` 标签加上：

```shell
<activeProfiles>
  <activeProfile>jdk8</activeProfile>
</activeProfiles>
```



#### Jenkins

拉取 Jenkins 镜像

> 低版本Jenkins插件容易安装失败！！！

```shell
docker pull jenkins/jenkins
```

编写 docker-compse

`/usr/local/docker/jenkins_docker/docker-compose.yml`：

```yaml
version: '3.1'
services:
  gitlab:
    image: 'jenkins/jenkins'
    container_name: jenkins
    ports:
      - '8080:8080'
      - '50000:50000'
    volumes:
      - './data/:/var/jenkins_home'
```

创建容器

```shell
docker-compose up -d
```

跟踪日志

```shell
docker logs -f jenkins
```



> **报错显示权限不足：**
>
> <img src="/images/fragments/docker/docker-jenkins_step1.webp" />
>
> 加上权限
>
> ```shell
> chmod -R 777 data
> ```
>
> 重启
>
> ```shell
> docker-compose restart
> ```
>



得到密码

```shell
docker-compose logs -f jenkins
```

<img src="/images/fragments/docker/docker-jenkins_step2.webp" />



测试访问：

http://192.168.31.102:8080/

输入上一步得到的密码 > 选择插件来安装(默认) > 安装 > 继续...



> **插件安装失败**
>
> 1）可以尝试切换国内镜像站
>
> 修改 `/usr/local/docker/jenkins_docker/data/hudson.model.UpdateCenter.xml`
>
> ```xml
> <?xml version='1.1' encoding='UTF-8'?>
> <sites>
>   <site>
>     <id>default</id>
>     <url>https://mirror.esuni.jp/jenkins/updates/update-center.json</url>
>   </site>
> </sites>
> ```
>
> 2）如果还不能解决请使用最新版本Jenkins



#### 安装 Jenkins 插件

Git Parameter、Publish Over SSH

<img src="/images/fragments/docker/docker-jenkins_step3.webp" />



#### 配置 Jenkins

将JDK和Maven移动到Jenkins的data目录下

```shell
mv /usr/local/jdk/ /usr/local/docker/jenkins_docker/data/
mv /usr/local/maven/ /usr/local/docker/jenkins_docker/data/
```



进入容器

```shell
docker exec -it jenkins bash
```

得到容器内JDK、Maven路径

```tex
cd ~
ls	# 查看jdk,maven是否在该目录下
pwd	# 得到路径/var/jenkins_home
```



Jenkins指定JDK、Maven

> 用于让Jenkins拉取和构建代码

<img src="/images/fragments/docker/docker-jenkins_step4.webp" />

<img src="/images/fragments/docker/docker-jenkins_step5.webp" />



Jenkins指定Publish over SSH

> 用于让Jenkins将构建好的jar包推送目标服务器，并且通知目标服务器
>
> 提前在192.168.31.102服务器下创建好目录：/usr/local/test

<img src="/images/fragments/docker/docker-jenkins_step6.webp" />

<img src="/images/fragments/docker/docker-jenkins_step7.webp" />



#### 测试Jenkins

在GitLab上创建测试项目mytest

> 相关参考：[Docker安装GitLab](https://carpedx.com/fragment/docker-gitlab/)

<img src="/images/fragments/docker/docker-jenkins_step10.webp" />



Jenkins上创建测试任务

配置Git拉取代码

<img src="/images/fragments/docker/docker-jenkins_step8.webp" />

<img src="/images/fragments/docker/docker-jenkins_step9.webp" />

配置Maven构建jar包

<img src="/images/fragments/docker/docker-jenkins_step11.webp" />

<img src="/images/fragments/docker/docker-jenkins_step12.webp" />

> mvn clean package -DskipTests	清除之前的构建文件，然后编译、测试和打包项目，但在这个过程中跳过运行测试

配置SSH，将构建后的jar包放到目标服务器

<img src="/images/fragments/docker/docker-jenkins_step13.webp" />

<img src="/images/fragments/docker/docker-jenkins_step14.webp" />

