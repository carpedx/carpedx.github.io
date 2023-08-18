---
layout: post
title: PHP-CS-Fixer简单使用
categories: php
description: PHP-CS-Fixer简单使用
keywords: php
---



**php-cs-fixer 是个代码格式化工具，格式化的标准是 PSR-1、PSR-2 以及一些 symfony 的标准。**

PHP-CS-Fixer：[官方GitHub](https://github.com/PHP-CS-Fixer/PHP-CS-Fixer)，[官方文档](https://cs.symfony.com/)

------



#### 安装

```shell
composer require --dev friendsofphp/php-cs-fixer
```



#### 命令行使用

```shell
# 格式化目录 如果是当前目录的话可以省略
./vendor/bin/php-cs-fixer fix /path/to/dir
# 格式化文件
./vendor/bin/php-cs-fixer fix /path/to/file.php
```

参数：

- --verbose 用于展示应用了的规则

  ```shell
  --verbose
  ```

- --using-cache 不使用缓存

  ```shell
  --using-cache=no
  ```

- --config 指定配置文件

  ```shell
  --config=.php-cs-fixer.php
  ```

- --level 用于控制需要使用的规则层级（默认psr2）

  ```shell
  --level=psr0
  --level=psr1
  --level=psr2
  --level=symfony
  ```

- --fixers 默认情况下执行的是 `PSR-2` 的所有选项以及一些附加选项（主要是 symfony 相关的）。还有一些属于『贡献级别』的选项，你可以通过 `--fixers` 选择性的添加，`--fixers` 的多个条件要用逗号分开

  ```shell
  --fixers=linefeed,short_tag,indentation
  ```

- -name_of_fixer 设定禁用哪些选项。如果同时设定了 `--fixers` 和 `-name_of_fixer`，前者的优先级更高

- --dry-run 和 --diff 可以显示出需要修改的汇总，但是并不实际修改

  ```shell
  ./vendor/bin/php-cs-fixer fix --verbose --diff --dry-run
  ```



#### PHPStorm 配置 PHP-CS-Fixer 代码检查提示

接下来，在 PhpStorm 的 Preferences、Languages & Frameworks、PHP、Quality Tools 配置界面中，目前还没有配置任何 PHP CS Fixer 路径：

<img src="/images/posts/php/php_cs_fixer_use_step1.jpg" />

点击配置下拉框右侧的「...」按钮，在弹出的窗口输入框输入上面运行 `which php-cs-fixer` 命令返回的路径，点击「Validate」按钮进行验证：

<img src="/images/posts/php/php_cs_fixer_use_step2.jpg" />

下面会出现包含 OK 和 PHP CS Fixer 版本的提示文本，表示该路径有效，点击「Apply」按钮应用更改，点击「OK」关闭该窗口。

接下来，在 PHP、Quality Tools 界面点击「PHP CS Fixer inspection」：

<img src="/images/posts/php/php_cs_fixer_use_step3.jpg" />

在弹出界面勾选「PHP CS Fixer validation」：

<img src="/images/posts/php/php_cs_fixer_use_step4.jpg" />

可以看到这里默认使用的是 PSR-2 编码规则（你还可以通过下拉框选择使用其他编码风格）。点击「Apply」应用更改，点击「OK」关闭窗口。



#### PHPStorm 配置 PHP-CS-Fixer 自动修正代码

接下来，我们就可以在 PhpStorm 中通过上面配置的 PHP CS Fixer 对代码进行嗅探和自动修正了。

**单个文件**

我们打开一个 PHP 文件，将类和方法后面的花括号调整为不换行：

<img src="/images/posts/php/php_cs_fixer_use_step5.jpg" />

此时，可以看到代码下面出现波浪线，这意味着 PHP CS Fixer 嗅探到不符合系统设置编码风格的代码（这里是 PSR-2），将光标移动到出现问题的代码位置，停留片刻会出现提示框，提示类定义、方法定义的括号不符合指定编码风格：

<img src="/images/posts/php/php_cs_fixer_use_step6.jpg" />

你可以通过点击下面的蓝色小字「PHP CS Fixer：fix the whole file」自动修复这个文件（对应的快捷键是 Option + Shift + Enter）



**批量修正**

当然，对于整个项目来说，如果一个个这样修复是不现实的，我们可以在 PhpStorm 中通过配置外部工具来实现批量修正指定目录的代码风格。在 Preferences、Tools、External Tools 界面点击「+」新建一个外部工具：

<img src="/images/posts/php/php_cs_fixer_use_step7.jpg" />

- Name 自定义即可
- Program 如果是 `composer` 安装则选择 `composer` 下 `php-cs-fixer.bat` 所在的位置，Windows当前项目下一般为： `D:\project\vendor\bin\php-cs-fixer.bat`，linux/mac下一般为：`~/.composer/vendor/bin/php-cs-fixer`

- Arguments 

  ```shell
  --verbose fix "$FileDir$/$FileName$"
  ```

  > --config=D:\projects\PhpstormProjects\supports\.php-cs-fixer.php 默认会查找项目根目录下的 .php-cs-fixer 文件

- Working directory 工作目录

  ```shell
  $ProjectFileDir$
  ```



**配置快捷键**

<img src="/images/posts/php/php_cs_fixer_use_step8.jpg" />