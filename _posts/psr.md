---
layout: post
title: PHPStan简单使用
categories: php
description: PHPStan简单使用
keywords: php, phpstan
---



**`PSR` 是 `PHP Standard Recommendations` （PHP 推荐标准）的简写，是 PHP 开发的实践标准。**

PSR：[官方GitHub](https://github.com/php-fig)，[官方文档](https://www.php-fig.org/psr/)

------



#### PSR-0：自动加载标准

> **已弃用**- 自 2014 年 10 月 21 日起，PSR-0 已被标记为已弃用。现在推荐[PSR-4作为替代品。](https://www.php-fig.org/psr/psr-4/)



#### PSR-1：基本编码标准

- PHP 代码文件 **必须** 以 `<?php` 或 `<?=` 标签开始

- PHP 代码文件 **必须** 以 `不带 BOM 的 UTF-8` 编码

- PHP 代码文件 **应该** 要不就只定义声明，如类、函数或常量等。要不就只执行会产生 `副作用` 的逻辑操作。但 **不该** 同时具有两者

  反例：

  ```
  <?php
  // 「副作用」：修改 ini 配置
  ini_set('error_reporting', E_ALL);
  
  // 「副作用」：引入文件
  include "file.php";
  
  // 「副作用」：生成输出
  echo "<html>\n";
  
  // 声明函数
  function foo()
  {
      // function body
  }
  ```

  范例：

  ```
  <?php
  // 声明函数
  function foo()
  {
      // 函数主体部分
  }
  
  // 条件声明不属于「副作用」
  if (! function_exists('bar')) {
      function bar()
      {
          // 函数主体部分
      }
  }
  ```

- 命名空间以及类 必须 符合 PSR 的自动加载规范

  > 类名 **必须** 以类似 `StudlyCaps` 形式的大写开头的驼峰命名方式
  >
  > 每个类都独立为一个文件，并且至少在一个层次的命名空间内（比如例子中的类都必须在Vendor命名空间下）

  举个栗子：

  ```
  <?php
  // PHP 5.3 及更高版本：
  namespace Vendor\Model;
  
  class Foo
  {
  }
  ```

  PHP 5.2 及更低版本 **应该** 使用伪命名空间

  ```
  <?php
  // PHP 5.2.x 及更低版本：
  class Vendor_Model_Foo
  {
  }
  ```

- 类的常量、属性和方法

  - 常量中所有字母都 **必须** 大写，词间以下划线分隔
  - 类的属性命名 **可以** 遵循：大写开头的驼峰式 (`$StudlyCaps`)、小写开头的驼峰式 (`$camelCase`)、下划线分隔式 (`$under_score`)
  - 方法名称 **必须** 符合 `camelCase()` 式的小写开头驼峰命名规范




#### PSR-2：编码风格指南

> **已弃用**- 自 2019 年 8 月 10 日起，PSR-2 已被标记为已弃用。现在推荐[PSR-12作为替代品。](https://www.php-fig.org/psr/psr-12/)



#### PSR-3：日志接口规范

> 本文制定了日志类库的通用接口规范，通过接收一个 `Psr\Log\LoggerInterface` 对象，来记录日志信息。 

**规范**

- `LoggerInterface` 接口对外定义了八个方法，分别用来记录八个等级的日志：debug、 info、 notice、 warning、 error、 critical、 alert 以及 emergency 。
- log方法：其第一个参数为记录的等级

**消息**
