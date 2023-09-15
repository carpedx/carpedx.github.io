安装docker异常：

Error: Unable to find a match: docker-ce docker-ce-cli containerd.io

首先更新：yum -y update

centos8默认使用podman代替docker，所以需要containerd.io

```shell
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
```

安装一些其他依赖

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

安装docker

```shell
yum install -y docker-ce 
```

启动docker

```shell
systemctl start docker
```

https://blog.csdn.net/weixin_41725792/article/details/109679971