---
layout: fragment
title: SVN合并时忽略某些版本
tags: [svn]
description: SVN合并时忽略某些版本
keywords: svn
---



**问题描述：有两个项目：branches和trunk，目前项目trunk需要合并branches，但是不需要合并其中的6916到6918分支**

------



#### 打开SVN版本库浏览器，并导航到trunk项目目录

<img src="/images/fragments/svn/svn-merge-ignore-version_step1.jpg" />



#### 找到“显示属性”选项卡

<img src="/images/fragments/svn/svn-merge-ignore-version_step2.jpg" />



#### 配置属性即可

`svn:ignore`

```tex
..env
.env
.idea
runtime
```

`svn:mergeinfo`

> 编辑trunk的svn:mergeinfo，增加6916-6918即可

```tex
/carpedx/branches:31-116,1649-6914,6916-6918
```

