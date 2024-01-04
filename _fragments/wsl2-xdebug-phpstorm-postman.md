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

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step7.webp" />



**配置Postman：**

配置 cookie（`XDEBUG_SESSION`）：

```tex
XDEBUG_SESSION=PHPSTORM; Path=/; Expires=Sat, 17 Aug 2024 09:02:46 GMT;
```

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step5.webp" />

<img src="/images/fragments/php/wsl2-xdebug-phpstorm_step6.webp" />
