---
layout: fragment
title: PHP使用openssl des加密报错0308010C
tags: [svn]
description: PHP使用openssl des加密报错0308010C
keywords: svn
---



**原因是: 要在 OpenSSL 中启用 DES 加密，你需要使用 OpenSSL 库的高级版本，并且需要在编译时启用 DES 支持**

------



#### 修改 openssl.cnf 配置文件

Linux下，文件位置一般在 /etc/ssl/openssl.cnf

找到[provider_sect]并将其更改为以下内容：

```php
[provider_sect]
default = default_sect
legacy = legacy_sect
```

找到[default_sect]并将其更改为以下内容：

```php
[default_sect]
activate = 1
[legacy_sect]
activate = 1
```

如图所示: 

<img src="/images/fragments/php/php-openssl_step1.webp" />

需要重启 php-fpm
