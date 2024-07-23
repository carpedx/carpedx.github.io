---
layout: post
title: PHPStudy PHP8.2.9 扩展报错问题
tags: [php]
description: PHPStudy PHP8.2.9 扩展报错问题
keywords: php
---



**切换php版本到更高版本时报错如下：Fatal error: Directive 'track_errors' is no longer available in PHP in Unknown on line 0**

------



#### 1、找到对应版本的 `php.ini`

#### 2、查找搜索 `track_errors` 这个参数

#### 3、`track_errors` 的值 `On` 设置为 `Off`

<img src="/images/fragments/php/php-extended-error-reporting_step1.webp" />

#### 4、如果报错找不到 `php` 扩展

手动在 `php.ini` 中添加一行，用来指定 `php` 扩展的目录：

```php
extension_dir="D:\phpstudy_pro\Extensions\php\php8.2.9nts\ext"
```
