---
layout: post
title: MySQL快速生成千万条测试数据
categories: mysql
description: MySQL快速生成千万条测试数据
keywords: mysql
---



**MySQL快速生成千万条测试数据**

------



#### 实现思路

利用mysql内存表插入速度快的特点，先利用函数和存储过程在内存表中生成数据，然后再从内存表插入普通表中



#### 1）创建内存表和普通表

```mysql
#创建内存表
CREATE TABLE `test_user_memory` (
 `id` int(11) NOT NULL AUTO_INCREMENT comment '主键id',
 `user_id` varchar(36) NOT NULL  comment '用户id',
 `user_name` varchar(30) NOT NULL comment '用户名称',
 `phone` varchar(20) NOT NULL comment '手机号码',
 `lan_id` int(9) NOT NULL comment '本地网',
 `region_id` int(9) NOT NULL comment '区域',
 `create_time` datetime NOT NULL comment '创建时间',
 PRIMARY KEY (`id`),
 KEY `idx_user_id` (`user_id`)
) ENGINE=MEMORY DEFAULT CHARSET=utf8mb4;

#创建普通表
CREATE TABLE `test_user` (
 `id` int(11) NOT NULL AUTO_INCREMENT comment '主键id',
 `user_id` varchar(36) NOT NULL  comment '用户id',
 `user_name` varchar(30) NOT NULL comment '用户名称',
 `phone` varchar(20) NOT NULL comment '手机号码',
 `lan_id` int(9) NOT NULL comment '本地网',
 `region_id` int(9) NOT NULL comment '区域',
 `create_time` datetime NOT NULL comment '创建时间',
 PRIMARY KEY (`id`),
 KEY `idx_user_id` (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```



#### 2）创建函数及存储过程

**创建生成n个随机数字的函数**

生成手机号码的时候要用到

```mysql
#生成n个随机数字
DELIMITER $$
CREATE FUNCTION randNum(n int) RETURNS VARCHAR(255)
BEGIN
    DECLARE chars_str varchar(20) DEFAULT '0123456789';
    DECLARE return_str varchar(255) DEFAULT '';
    DECLARE i INT DEFAULT 0;
    WHILE i < n DO
        SET return_str = concat(return_str,substring(chars_str , FLOOR(1 + RAND()*10 ),1));
        SET i = i +1;
    END WHILE;
    RETURN return_str;
END $$
DELIMITER;
```



**创建生成号码函数**

```mysql
#生成随机手机号码
# 定义常用的手机头 130 131 132 133 134 135 136 137 138 139 186 187 189 151 157
#SET starts = 1+floor(rand()*15)*4;   截取字符串的开始是从 1、5、9、13 ...开始的。floor(rand()*15)的取值范围是0~14
#SET head = substring(bodys,starts,3);在字符串bodys中从starts位置截取三位

DELIMITER $$
CREATE FUNCTION generatePhone() RETURNS varchar(20)
BEGIN
DECLARE head char(3);
DECLARE phone varchar(20);
DECLARE bodys varchar(100) default "130 131 132 133 134 135 136 137 138 139 186 187 189 151 157";
DECLARE starts int;
SET starts = 1+floor(rand()*15)*4;  
SET head = trim(substring(bodys,starts,3));  
SET phone = trim(concat(head,randNum(8)));
RETURN phone;
END $$
DELIMITER ;
```



**创建随机字符串函数**

```mysql
#创建随机字符串和随机时间的函数
DELIMITER $$
CREATE FUNCTION `randStr`(n INT) RETURNS varchar(255) CHARSET utf8mb4
DETERMINISTIC
BEGIN
 DECLARE chars_str varchar(100) DEFAULT 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
 DECLARE return_str varchar(255) DEFAULT '' ;
 DECLARE i INT DEFAULT 0;
 WHILE i < n DO
  SET return_str = concat(return_str, substring(chars_str, FLOOR(1 + RAND() * 62), 1));
  SET i = i + 1;
 END WHILE;
 RETURN return_str;
 END$$
 DELIMITER;
```



**创建插入内存表数据的存储过程**

```mysql
# 创建插入内存表数据存储过程   入参n是多少就插入多少条数据
DELIMITER $$
CREATE PROCEDURE `add_test_user_memory`(IN n int)
 BEGIN
 DECLARE i INT DEFAULT 1;
 WHILE (i <= n) DO
  INSERT INTO test_user_memory (user_id, user_name, phone, lan_id,region_id, create_time) VALUES (uuid(), randStr(20), generatePhone(), FLOOR(RAND() * 1000), FLOOR(RAND() * 100), NOW());
  SET i = i + 1;
 END WHILE;
 END $$
 DELIMITER ;
```



**创建内存表数据插入普通表存储过程**

此处利用对内存表的循环插入和删除来实现批量生成数据，这样可以不需要更改mysql默认的max_heap_table_size值也照样可以生成百万或者千万的数据。 max_heap_table_size默认值是16M。 max_heap_table_size的作用是配置用户创建内存临时表的大小，配置的值越大，能存进内存表的数据就越多。

```mysql
#循环从内存表获取数据插入普通表
#参数描述 n表示循环调用几次；count表示每次插入内存表和普通表的数据量
 DELIMITER $$
 CREATE PROCEDURE `add_test_user_memory_to_outside`(IN n int, IN count int)
 BEGIN
 DECLARE i INT DEFAULT 1;
 WHILE (i <= n) DO
  CALL add_test_user_memory(count);
 INSERT INTO test_user SELECT * FROM test_user_memory;
 delete from test_user_memory;
 SET i = i + 1;
 END WHILE;
 END $$
 DELIMITER ;
```



#### 3）调用存储过程插入数据

```mysql
#先调用存储过程往内存表插入一万条数据，然后再把内存表的一万条数据插入普通表
CALL add_test_user_memory(10000);
#一次性把内存表的数据插入到普通表，这个过程是很快的
INSERT INTO test_user SELECT * FROM test_user_memory;
#清空内存表数据
delete from test_user_memory;
```



#### 修改mysql内存表存储大小的值

**1、通过执行mysql命令修改**

```mysql
SET GLOBAL tmp_table_size=2147483648;
SET GLOBAL max_heap_table_size=2147483648;
```



**2、通过修改mysql配置文件**

```mysql
vi /etc/my.cnf
[mysqld]
max_heap_table_size = 2048M
tmp_table_size = 2048M
```



**3、查看内存表存储大小**

```mysql
SHOW VARIABLES LIKE 'max_heap_table_size';
```



#### 调用另一个存储过程

**add_test_user_memory_to_outside**

这个存储过程就是通过不断循环插入内存表，再从内存表获取数据插入普通表，最后删除内存表，以此循环直至循环结束。

```mysql
#循环100次，每次生成10000条数据 总共生成一百万条数据
CALL add_test_user_memory_to_outside(100,10000);
```

