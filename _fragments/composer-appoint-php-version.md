---
layout: fragment
title: 指定composer使用的php版本
tags: [composer, php]
description: 指定composer使用的php版本
keywords: composer, php
---



**解决不想破坏原有环境变量，但是使用 `composer require` 安装时提示php版本过低的问题**



## 方案一（修改环境变量）

#### composer 使用的是 "环境变量" 里面的php，所以使用 `php -v` 查看系统php版本，切换php版本可以修改环境变量后重启

> 太麻烦了，每次都要修改环境变量，还要重起



## 方案二（下载Composer）

#### 下载Composer [[官方下载链接]](https://getcomposer.org/download/)

#### 将composer.phar复制到项目根目录，比如我的是：D:\projects\git\carpedx.github.io

#### 执行如下命令：

```shell
D:\develop\phpstudy_pro\Extensions\php\php8.0.2nts\php.exe composer.phar update
```



## 方案三（两份composer.bat）

#### 首先下载Composer

#### 复制一份 `composer.bat` 并改名为 `composer8.bat`， 指定php8绝对地址

composer8.bat内容如下：

```shell
@echo OFF
:: in case DelayedExpansion is on and a path contains ! 
setlocal DISABLEDELAYEDEXPANSION
D:\develop\phpstudy_pro\Extensions\php\php8.0.2nts\php.exe "%~dp0composer.phar" %*
```

#### 运行时使用：`composer8 update` 命令，旧的php版本依然使用：`composer update` 命令
