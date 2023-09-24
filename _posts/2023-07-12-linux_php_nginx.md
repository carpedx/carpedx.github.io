---
layout: post
title: Linux安装PHP、Nginx
categories: linux
description: Linux安装PHP、Nginx
keywords: linux, php, nginx
---





**Linux安装PHP、Nginx**

------



## PHP



#### Centos7 使用 yum 安装 PHP7.4

添加EPEL和REMI存储库:

```shell
yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm  
yum -y install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

安装PHP 7.4 Remi存储库并启用PHP 7.4版本

```shell
yum-config-manager --enable remi-php74  
yum update  
yum install php
```



#### Centos7 安装 PHP 相关扩展

```shell
yum install php php-fpm php-mysql php-mbstring php-mcrypt php-sockets php-curl php-common php-xml php-soap
```

**php-fpm**

启动 `php-fpm` ，设置开机启动

```shell
systemctl start php-fpm
systemctl enable php-fpm
```

修改 `php-fpm` 端口

```shell
vim /etc/php-fpm.d/www.conf
# 想要改成10001端口
# 将 listen = 127.0.0.1:9000 改成 listen = 10001 即可
```

查看 `php-fpm` 启动状态

```shell
service php-fpm status
```





#### Centos8 使用 dnf 安装 PHP7.4

确保已更新到最新版本

```shell
dnf update
```

安装PHP 7.4的基本包

```shell
dnf install php74
```

验证PHP的安装

```shell
php74 -v
```

如果您需要安装PHP的其他扩展或工具，可以使用类似的方法安装。例如，要安装PHP的MySQL扩展，可以运行以下命令：

```shell
dnf install php74-php-mysqlnd
```

如果您需要将PHP 7.4设置为默认版本的PHP，可以运行以下命令：

```shell
dnf install php74-php-default
```

安装完成后，您可以使用以下命令来验证默认版本的PHP：

```shell
# 这将显示默认版本的PHP的版本信息
php -v
```



## Nginx



#### Centos7 使用 yum 安装 Nginx

安装gcc，因为Nginx需要C语言支持

```shell
yum install gcc
```

安装pcre库，因为Nginx需要perl兼容的正则表达式库

```shell
yum install pcre pcre-devel
```

安装Nginx

```shell
yum install nginx
```

> 安装目录：/etc/nginx

启动Nginx服务

```shell
systemctl start nginx
```

设置Nginx开机自启动

```shell
systemctl enable nginx
```

