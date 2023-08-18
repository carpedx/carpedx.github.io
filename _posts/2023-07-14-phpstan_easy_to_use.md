---
layout: post
title: PHPStan简单使用
categories: php
description: PHPStan简单使用
keywords: php, phpstan
---



**PHPStan无需编写测试用例，即可发现代码中的错误**

> 使用PHPStan要求PHP >= 7.2

PHPStan：[官方GitHub](https://github.com/phpstan/phpstan)，[官方文档](https://phpstan.org/)

------



#### 安装

```shell
composer require --dev phpstan/phpstan
```



#### 分析代码

```shell
vendor\bin\phpstan analyse [options] [<paths>...]
```

可以传递一个或多个 PHP 文件或目录的路径，并用空格分隔。例：

```shell
vendor\bin\phpstan analyse src tests
```

**options：**

指定要运行的规则级别

- --level 或 -l

> 0 是最宽松的，9 是最严格的

```shell
vendor\bin\phpstan analyse -l src tests
```

指定配置文件的路径

- --configuration 或 -c

  > PHPStan使用名为NEON的配置格式。[[配置参考]](https://phpstan.org/config-reference)

```shell
vendor\bin\phpstan analyse -c phpstan.neon
```

生产文件基线

-  --generate-baseline 或 -b

  > 将当前报告的错误列表声明为“基线”，并使其在后续运行中不被报告

```shell
vendor\bin\phpstan analyse --level 7 \
  --configuration phpstan.neon \
  src tests --generate-baseline
```

自定义自动加载器

- --autoload-file 或 -a

  > 如果您的依赖项安装在不同的路径上，或者您正在从不同的目录运行PHPStan，您可以使用--autooload-file来指定自动加载程序的路径

```shell
vendor\bin\phpstan analyse \
  --autoload-file=/path/to/autoload.php \
  src tests
```

自定义错误格式化

- --error-format

  > 支持不同的输出格式。[了解有关输出格式更多信息](https://phpstan.org/user-guide/output-format)

```tex
您可以将以下关键字传递给命令--error-format=X 以影响输出
```

指定内存限制

- --memory-limit

```shell
--memory-limit 1G
```

启用 XDebug

- --xdebug

  > 如果您需要调试 PHPStan 本身或自定义扩展，并希望在启用 XDebug 的情况下运行 PHPStan

分析之前输出每个分析文件的行

- --debug

  > 会在出现第一个内部错误时停止并打印堆栈跟踪

输出颜色以及进度条颜色

- --ansi, --no-ansi

不显示输出

- --quiet 或 -q

输出当前使用的 PHPStan 版本

- --version 或 -V

清除结果缓存

- clear-result-cache

输出可用选项

- --help



#### 不带参数运行

vendor/bin/phpstan不带参数运行需要满足以下条件：

- 您有phpstan.neon或phpstan.neon.dist在您当前的工作目录中
- 该文件包含paths用于设置分析路径列表的参数
- 该文件包含level设置当前规则级别的参数

**phpstan.neon:**

```properties
parameters:
	level: 6
	paths:
		- src
		- tests
```

