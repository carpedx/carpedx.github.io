---
layout: fragment
title: 解决“无法使用Subversion命令行运行客户端”
tags: [php]
description: 解决“无法使用Subversion命令行运行客户端”
keywords: php
---



**解决“无法使用Subversion命令行运行客户端” **

------



#### 问题如下

<img src="/images/fragments/php/phpstorm-subversion-error_step1.webp" />



## 解决方法

**更新TortoiseSVN**

默认情况下，命令行工具没有被安装，再启动一次安装程序选择Modify

<img src="/images/fragments/php/phpstorm-subversion-error_step2.webp" />

<img src="/images/fragments/php/phpstorm-subversion-error_step3.webp" />

<img src="/images/fragments/php/phpstorm-subversion-error_step4.webp" />



**配置PhpStorm**

再启动Webstorm之后，就有`svn.exe`了

<img src="/images/fragments/php/phpstorm-subversion-error_step5.webp" />

选择确定之后，问题解决
