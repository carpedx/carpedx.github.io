---
layout: post
title: Excel表格中将多行数据转换为加单引号的逗号分隔数据
categories: excel
description: Excel表格中将多行数据转换为加单引号的逗号分隔数据
keywords: excel
---

** 有时我们再写SQL IN查询的时候需要将数据单引号加逗号分隔 **

#### 实例：一组Excel手机号数据，需要你在数据库中SQL IN查询
<img src="/images/posts/excel/mysql_multi_row_data_conversion_step1.webp" width="80%" />

** 第一步：加单引号和逗号 ** 
```
="'"&A1&"',"
```
<img src="/images/posts/excel/mysql_multi_row_data_conversion_step2.webp" width="80%" alt="步骤一" />

** 第二步： 复制粘贴值，只保留值 **
<img src="/images/posts/excel/mysql_multi_row_data_conversion_step3.webp" width="80%" alt="步骤二" />

** 第三步：使用函数 PHONETIC 拼接为一个单元格 **
```
=PHONETIC(C1:C100)
```
<img src="/images/posts/excel/mysql_multi_row_data_conversion_step4.webp" width="80%" alt="步骤三" />

** 第四步：复制值，删掉最后一位逗号就可以直接放到 SQL IN 里查询了 **