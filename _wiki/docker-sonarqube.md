---
layout: wiki
title: Docker安装SonarQube
cate1: devops
cate2: 
description: Docker安装代码质量检查工具SonarQube
keywords: devops
sorting: 2
---



**SonarQube 代码质量检查工具**

> 相关文章：[Docker安装GitLab](https://carpedx.com/wiki/docker-gitlab/)、[Docker安装Jenkins](https://carpedx.com/wiki/docker-jenkins/)

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

> 注意sonarqube的版本，高版本已经不兼容jdk1.8

```shell
docker pull sonarqube:8.9.6-community
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
    image: 'sonarqube:8.9.6-community'
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

下载中文插件：

<img src="/images/wiki/docker/docker-sonarqube_step3.webp" />




> **报错显示内存不足：**
>
> <img src="/images/wiki/docker/docker-sonarqube_step2.webp" />
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
> <img src="/images/wiki/docker/docker-sonarqube_step1.webp" />
>
> 修改生效：
>
> ```shell
> sysctl -p
> ```



测试访问：

http://192.168.31.102:9000/	默认账号admin，密码admin



#### 使用Maven进行代码质量检测

1）修改 Maven 的 `conf/settings.xml` 文件：

> profiles、activeProfiles 标签下新增 sonar 配置

```xml
  </profiles>
    <profile>
      <id>jdk8</id>
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

	<profile>
	  <id>sonar</id>
	  <activation>
	    <activeByDefault>true</activeByDefault>
	  </activation>
	  <properties>
	    <sonar.login>admin</sonar.login>
		<sonar.password>password</sonar.password>
		<sonar.host.url>http://192.168.31.102:9000</sonar.host.url>
	  </properties>
	</profile>
  </profiles>

  <activeProfiles>
    <activeProfile>jdk8</activeProfile>
    <activeProfile>sonar</activeProfile>
  </activeProfiles>
```

<img src="/images/wiki/docker/docker-sonarqube_step4.webp" />

2）mytest项目下的控制台执行代码质量检测：

```shell
mvn clean
mvn compile
mvn sonar:sonar
```

3）执行成功后访问 http://192.168.31.102:9000/ 会显示检测结果

<img src="/images/wiki/docker/docker-sonarqube_step5.webp" />



#### [Sonar-Scanner](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.6.1.2450-linux.zip) 代码质量检测

下载unzip，用于解压zip文件

```shell
yum -y install unzip
```

将 sonar-scanner-cli-4.6.0.2311-linux.zip 放到 /root 下

```shell
unzip /root/sonar-scanner-cli-4.6.0.2311-linux.zip
```

目录名字改短

```shell
mv /root/sonar-scanner-4.6.0.2311-linux/ /root/sonar-scanner/
```

将 sonar-scanner 移动到 jenkins_docker/data/ 数据卷下

```shell
mv /root/sonar-scanner/ /usr/local/docker/jenkins_docker/data/
```

修改sonar-scanner配置文件

```shell
vim /usr/local/docker/jenkins_docker/data/sonar-scanner/conf/sonar-scanner.properties
```

<img src="/images/wiki/docker/docker-sonarqube_step6.webp" />

在 Sonarqube（http://192.168.31.102:9000/） 上生成 token

<img src="/images/wiki/docker/docker-sonarqube_step7.webp" />

进入 Jenkins 下 mytest 目录测试 sonar-scanner

```shell
cd /usr/local/docker/jenkins_docker/data/workspace/mytest
```

```shell
/usr/local/docker/jenkins_docker/data/sonar-scanner/bin/sonar-scanner -Dsonar.sources=./ -Dsonar.projectname=linux-mytest -Dsonar.login=366824812ef5c81172bbddbe7e4faed4a2684314 -Dsonar.projectKey=linux-mytest -Dsonar.java.binaries=./target/
```

执行成功后访问 http://192.168.31.102:9000/ 会显示检测结果

<img src="/images/wiki/docker/docker-sonarqube_step8.webp" />



#### Sonarqube 整合 Jenkins

Jenkins 下载 SonarQube Scanner 插件：

<img src="/images/wiki/docker/docker-sonarqube_step9.webp" />

Jenkins 系统管理 - 系统配置 SonarQube servers：

> 添加按钮点击没反应可以尝试写完Name、URL之后 应用 保存 重新进入该页面。如果还不行可以尝试重启 Jenkins：http://192.168.31.102:8080/restart

<img src="/images/wiki/docker/docker-sonarqube_step10.webp" />

<img src="/images/wiki/docker/docker-sonarqube_step11.webp" />

<img src="/images/wiki/docker/docker-sonarqube_step12.webp" />

<img src="/images/wiki/docker/docker-sonarqube_step13.webp" />

Jenkins 系统管理 - 全局配置 SonarQube Scanner：

<img src="/images/wiki/docker/docker-sonarqube_step14.webp" />

Jenkins 项目下增加构建步骤：

<img src="/images/wiki/docker/docker-sonarqube_step15.webp" />

<img src="/images/wiki/docker/docker-sonarqube_step16.webp" />

```shell
sonar.projectname=${JOB_NAME}
sonar.projectKey=${JOB_NAME}
sonar.source=./
sonar.java.binaries=target
```

删除 jenkins 下 .scannerwork 文件夹：

```shell
rm -rf /usr/local/docker/jenkins_docker/data/workspace/mytest/.scannerwork/
```

在 Jenkins 中再次构建，查看日志是否有 sonarqube：

<img src="/images/wiki/docker/docker-sonarqube_step17.webp" />
