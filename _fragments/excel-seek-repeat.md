---
layout: fragment
title: Excel查找两列重复项
tags: [excel]
description: Excel查找两列重复项
keywords: excel
---



**Excel在同一列有相同数据的情况下，查找两列相同的数据，但不包含同一列的重复**

相关文章：[Excel截取前后字符串操作](https://carpedx.com/fragment/excel-string-before-and-after-truncation/)

------

公式：

```shell
=AND(COUNTIF($A$1:$A1,A1)<=COUNTIF($B:$B,A1),A1<>"")
```

实例图：

<img src="/images/fragments/excel/excel-seek-repeat_step1.jpg" />
