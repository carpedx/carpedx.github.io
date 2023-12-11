---
layout: fragment
title: Docker下配置Jenkins流水线任务
tags: [docker]
description: Docker下配置Jenkins流水线任务
keywords: docker
---



相关文章：[Docker安装GitLab](https://carpedx.com/fragment/docker-gitlab/)、[Docker安装Jenkins](https://carpedx.com/fragment/docker-jenkins/)、[Docker安装SonarQube](https://carpedx.com/fragment/docker-sonarqube/)、[Docker安装Harbor](https://carpedx.com/fragment/docker-harbor/)

------



#### 配置项目

项目中增加 `Jenkinsfile` 文件，提交到 GitLab

<img src="/images/fragments/docker/docker-jenkins-pipeline_step1.webp" />

`Jenkinsfile`

```tex
// 所有的脚本命令都放在pipeline中
pipeline {
	// 指定任务在那个集群节点中执行
	agent any

	// 声明全局变量，方便后面使用
	environment {
		harborUser = 'admin'
		harborPassword = 'Harbor12345'
		harborAddress = '192.168.31.102:80'
		harborRepo = 'repo'
	}

	stages {
		stage('拉取git仓库代码') {
			steps {
				checkout scmGit(branches: [[name: '${tag}']], extensions: [], userRemoteConfigs: [[url: 'http://192.168.31.101:8929/root/mytest.git']])
			}
		}

		stage('通过maven构建项目') {
			steps {
				sh '/var/jenkins_home/maven/bin/mvn clean package -DskipTests'
			}
		}

		stage('通过SonarQube做代码质量检测') {
			steps {
				sh '/var/jenkins_home/sonar-scanner/bin/sonar-scanner -Dsonar.source=./ -Dsonar.projectname=${JOB_NAME} -Dsonar.projectKey=${JOB_NAME} -Dsonar.java.binaries=./target/ -Dsonar.login=e58e820539d2c6a42b2c84ca15e4f449c6d628c4'
			}
		}

		stage('通过Docker制作自定义镜像') {
			steps {
				sh '''mv ./target/*.jar ./docker/
				docker build -t ${JOB_NAME}:${tag} ./docker/'''
			}
		}

		stage('将自定义镜像推送到Harbor') {
			steps {
				sh '''docker login -u ${harborUser} -p ${harborPassword} ${harborAddress}
				docker tag ${JOB_NAME}:${tag} ${harborAddress}/${harborRepo}/${JOB_NAME}:${tag} 
				docker push ${harborAddress}/${harborRepo}/${JOB_NAME}:${tag} '''
			}
		}

		stage('通过Publish Over SSH通知目标服务器') {
			steps {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'test', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: "deploy.sh $harborAddress $harborRepo $JOB_NAME $tag $container_port $host_port", execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
			}
		}
	}
}
```

> 在 Jenkins 的 流水线语法 中可以生成这些代码：
>
> <img src="/images/fragments/docker/docker-jenkins-pipeline_step6.webp" />



#### 配置 Jenkins

Jenkins 新建流水线任务，配置 GitLab、参数

<img src="/images/fragments/docker/docker-jenkins-pipeline_step2.webp" />

配置 GitLab 地址：

<img src="/images/fragments/docker/docker-jenkins-pipeline_step3.webp" />

配置参数：

<img src="/images/fragments/docker/docker-jenkins-pipeline_step4.webp" />

<img src="/images/fragments/docker/docker-jenkins-pipeline_step5.webp" />



#### 测试构建

<img src="/images/fragments/docker/docker-jenkins-pipeline_step7.webp" />

<img src="/images/fragments/docker/docker-jenkins-pipeline_step8.webp" />



#### 常见报错

> Error response from daemon: login attempt to url failed with status: 502 Bad Gateway
>
> 检查 harbor 服务，是否又服务未启动，重启即可：
> <img src="/images/fragments/docker/docker-jenkins-pipeline_step9.webp" />
> <img src="/images/fragments/docker/docker-jenkins-pipeline_step10.webp" />



> permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "url": dial unix /var/run/docker.sock: connect: permission denied
>
> 权限不足，修改 docker.sock 的权限，更详细可参考 [Docker安装Harbor](https://carpedx.com/fragment/docker-harbor/)：
> `cd /var/run`
> `chown root:root docker.sock`
> `chmod o+rw docker.sock`