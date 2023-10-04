---
layout: wiki
title: 找出现奇数次的数
cate1: algorithm
cate2: 
description: 在一个数组中已知只有一种数出现了奇数次，其他的所有数都出现了偶数次？
keywords: algorithm
---



**在一个数组中已知只有一种数出现了奇数次，其他的所有数都出现了偶数次？**

**问题一：怎么找到出现了奇数次的数？**

**问题二：如果有两种数出现了奇数次，如何找到？**

**要求时间复杂度 O(N)，空间复杂度 O(1)**

------



#### 实现逻辑

使用异或运算符 `^`  的特性实现：

```tex
1）不同为1 相同为0，如：1^1=0，0^0=0，1^0=1
2）无进位相加，如：二进制`0011+0101=0110` 转换成十进制就是`5^3=6`
3）0和任何数异或都为N，如：0^N=N
4）任何数和自己异或都为0，如：N^N=0
5）异或运算与顺序无关，谁先谁后都可，只要是同一批数结果都一样，如：a^b=b^a
```

另外需要注意的是 `eor & (~eor + 1)`：

```tex
~eor：对eor按位取反。这意味着在~eor中1变0，0变1
~eor + 1：对~eor加1。考虑二进制数的性质，这实际上是将~eor最右侧的0变为1
eor & (~eor + 1)：按位与运算的性质，只有当两个操作数的对应位都为1时，结果的对应位才为1，但是在前面已经对~eor取反了，只有+1之后的最右侧一位为1

综上所述，表达式 eor & (~eor + 1) 可以取出 eor 最右侧的1
```



#### java

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
    int rightOne = eor & (~eor + 1);	// 提取出最右侧的1

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

