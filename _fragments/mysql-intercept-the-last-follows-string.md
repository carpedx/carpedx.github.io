---
layout: fragment
title: MySQL查询时截取某个字符最后一次出现位置后面的数据
tags: [mysql]
description: MySQL查询时截取某个字符最后一次出现位置后面的数据
keywords: mysql
---



#### 截取指定字符串 `前面` 的字符

如：

```tex
1333333952_3979_375x500.jpg 字符串中获取最后一个下划线(_)后面的数据
```

使用：

```sql
SELECT REVERSE(LEFT(REVERSE('1333333952_3979_375x500.jpg'),LOCATE('_',REVERSE('1333333952_3979_375x500.jpg'))-1));
```

该方法使用到的mysql函数：

```tex
left (被截取字段, 截取长度) 从左边截取
REVERSE (str) 字符串翻转
LOCATE (查找字符串, 查找位置) 查找字符串首次出现的位置
```

> 这只是我个人的思路。给大家抛个砖而已。希望大家可以举一反三
