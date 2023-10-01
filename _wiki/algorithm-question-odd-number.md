---
layout: wiki
title: 找出现奇数次的数
cate1: algorithm
cate2: 
description: 找出现奇数次的数
keywords: algorithm
---



**在一个数组中已知只有一种数出现了奇数次，其他的所有数都出现了偶数次。**

**问题一：怎么找到出现了奇数次的数？**

**问题二：如果有两种数出现了奇数次，如何找到？**

**要求时间复杂度 O(N)，空间复杂度 O(1) **

------



**java**

```java
public static void main(String[] args) {
    int[] arr1 = {64, 25, 12, 22, 11, 11, 22, 11, 11, 25, 64};
    // 调用问题一的函数，输出出现奇数次的数字
    question1(arr1);

    int[] arr2 = {64, 25, 12, 22, 11, 11, 22, 11, 11, 25, 64, 18};
    // 调用问题二的函数，输出出现奇数次的两个数字
    question2(arr2);
}

// 问题一的函数，通过异或运算找出出现奇数次的数字
public static void question1(int[] arr) {
	// 初始化异或结果为0 
    int eor = 0;
    // 遍历数组，对每个元素进行异或运算
    for (int cur : arr) {
        eor ^= cur;
    }
    // 输出异或结果，即为出现奇数次的数字  
    System.out.println("问题一答案：" + eor);
}

// 问题二的函数，通过异或运算找出出现奇数次的两个数字
public static void question2(int[] arr) {
    // 初始化异或结果为0 
    int eor = 0;
    // 遍历数组，对每个元素进行异或运算 
    for (int cur : arr) {
        eor ^= cur;
    }
    // eor = a ^ b，a和b为出现奇数次的两个数字  
    // eor != 0，因为a和b不相等，所以至少有一个位置上是1  
    // eor必然有一个位置上是1，提取出最右侧的1  
    int rightOne = eor & (~eor + 1);    // 提取出最右侧的1  

    // 初始化onlyOne为0，用于存储其中一个出现奇数次的数字
    int onlyOne = 0;
    // 遍历数组，对每个元素进行判断  
    for (int cur : arr) {
        // 如果当前元素与rightOne进行与运算的结果不为0，说明当前元素在rightOne对应的位置上是1  
        // 对onlyOne进行异或运算，将其更新为a或b中的一个数字  
        if ((cur & rightOne) != 0) {
            onlyOne ^= cur;
        }
    }

    // 输出onlyOne和eor ^ onlyOne，即为出现奇数次的两个数字 
    System.out.println("问题二答案：" + onlyOne + "，" + (eor ^ onlyOne));
}
```

