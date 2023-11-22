---
layout: fragment
title: Excel截取前后字符串操作
tags: [excel]
description: Excel截取前后字符串操作
keywords: excel
---



相关文章：[Excel查找两列重复项](https://carpedx.com/fragment/excel-seek-repeat/)

------



#### 截取指定字符串 `前面` 的字符

如：

```tex
<img src=""/><br/>carpedx
想得到<br/>前面的字符（不包括<br/>）
```

使用：

```shell
=LEFT(A1,FIND("<br/>",A1)-1)
```

会得到：

```tex
<img src=""/>
```



#### 截取指定字符串 `后面` 的字符

如：

```tex
<img src=""/><br/>carpedx
想得到<br/>后面的字符（不包括<br/>）
```

使用：

```shell
=MID(A1,FIND("<br/>",A1)+5,50)
```

会得到：

```tex
carpedx
```

