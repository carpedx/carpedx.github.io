---
layout: note
title: JetBrains全家桶的安装与激活
categories: JetBrains
description: JetBrains全家桶的安装与激活
keywords: JetBrains
---



**JetBrains全家桶的安装与激活**

------



#### 激活步骤：

1）打开[激活网址](https://www.jiweichengzhu.com/ide/code)



2）关注公众号：雨落无影，发送消息 `下载` 得到下载码



3）下载文件ja-netfilter.v3.1.zip
<img src="/images/notes/jetbrains/jetbrains_active_step1.jpg" />



4）下载激活码
<img src="/images/notes/jetbrains/jetbrains_active_step2.jpg" />



5）去官网下载[源文件](https://www.jetbrains.com.cn/phpstorm/promo/?utm_source=baidu&utm_medium=cpc&utm_campaign=cn-bai-br-phpstorm-ex-pc&utm_content=phpstorm-prue&utm_term=phpstorm&bd_vid=8178875924755282455)



6）将源文件安装到电脑中，安装完不要点击运行



7）将phpstorm中的bin开始文件.vmoptions文件加入固定参数 和 激活用的jia包

```tex
--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED
--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED
-javaagent:D:/develop/JetBrains/ja-netfilter.v3.1/ja-netfilter.jar
```



8）点击运行软件，输入我们已经下好的激活码。
<img src="/images/notes/jetbrains/jetbrains_active_step3.jpg" />