---
layout: fragment
title: WSL2+XDebug+PHPStorm配置postman调试
tags: [php]
description: WSL2+XDebug+PHPStorm配置postman调试
keywords: php
---



**安装 PHP Xdebug 扩展：**

 1. `php --info` 找到 `php.ini` 文件

 2. 设置 `php.ini`

    > 有apache2，则apache2下的php.ini也需要配置

    ```shell
    [Xdebug]
    zend_extension=xdebug.so
    xdebug.mode=debug
    xdebug.discover_client_host=true
    xdebug.client_port=9003
    xdebug.max_nesting_level=512
    xdebug.remote_enable=1
    xdebug.remote_connect_back=1
    xdebug.idekey=PHPSTORM
    xdebug.remote_autostart=1
    xdebug.client_host=127.0.0.1
    xdebug.remote_handler=dbgp
    xdebug.start_with_request=trigger
    xdebug.remote_autostart=1
    ```



**配置PHPStorm：**

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step1.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step2.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step3.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step4.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step5.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step6.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step7.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step8.webp" />



**配置Postman：**

配置 cookie（`XDEBUG_SESSION`）：

```tex
XDEBUG_SESSION=PHPSTORM; Path=/; Expires=Sat, 17 Aug 2024 09:02:46 GMT;
```

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step9.webp" />



#### 注意点：

1）PHPStorm 配置中的 `PHP -> Servers` 中的端口号为：`当前项目端口号` ，并且需要注意：`映射文件为项目绝对路径 如 /www/wwwroot/admin` ，还需要注意 `NAME` 与 `php.ini` 中的 XDebug 配置保持一致

2）PHPStorm 配置中的 `PHP -> Debug -> DBGp Proxy` 中的端口号为 XDebug的端口号 ，还需要注意 `IDE key` 与 `php.ini` 中的 XDebug 配置保持一致

3）Postman 配置 Cookies 的 `XDEBUG_SESSION=[名称]` ，【名称】需要与 `php.ini` 中的 XDebug 配置保持一致

4）验证 XDebug 的 validate 时，需要启动项目并指定到 `public` 文件
