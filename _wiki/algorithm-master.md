---
layout: wiki
title: Master公式和二分查找算法
cate1: algorithm
cate2: 
description: Master公式和二分查找算法
keywords: algorithm
---



**Master公式是一种用于解决递归问题时间复杂度的公式，它可以帮助我们快速估算递归算法的时间复杂度**

------



#### 二分查找最大值

```java
public static void main(String[] args) {  
    // 初始化一个整型数组  
    int[] arr = {64, 25, 12, 22, 11};  
    // 调用getMax函数，打印出数组中的最大值  
    System.out.println(getMax(arr));  
}  

// getMax函数，接收一个整型数组作为参数，返回数组中的最大值  
public static int getMax(int[] arr) {  
    // 调用process函数，传入数组和范围参数，返回最大值  
    return process(arr, 0, arr.length - 1);  
}  

// 接收一个整型数组和两个整数L、R作为参数，返回数组中[L...R]范围内的最大值  
public static int process(int[] arr, int L, int R) {  
    // 当范围只有一个元素时，直接返回该元素作为最大值
    if (L == R) return arr[L];  

    // 计算范围的中间点索引，使用位运算加速计算
    int mid = L + ((R -L) >> 1);    // 中间点索引
    // 递归调用process函数，求解左半部分[L...mid]的最大值
    int leftMax = process(arr, L, mid);  
    // 递归调用process函数，求解右半部分[mid+1...R]的最大值  
    int rightMax = process(arr, mid + 1, R); 
    
    // 得到左右两部分的最大值
    return Math.max(leftMax, rightMax);  
}
```



#### Master公式

计算公式：`T(N)=a*T(N/b)+O(N^d)`

> T(N) 表示母问题和数据规模，比如在上面二分查找中就是 `public static int process(int[] arr, int L, int R)`
>
> a 表示子问题被调用次数，比如在上面二分查找中就是 2，因为被调用了两次 `int leftMax = process(arr, L, mid);`  和 `int rightMax = process(arr, mid + 1, R);`
>
> T(N/b) 表示子问题和数据规模，比如在上面二分查找中就是 `process(arr, L, mid)` 和 `process(arr, mid + 1, R)` ，b为2，因为都是二分之一数据量
>
> O(N^d) 除了子问题的调用之外，剩下的过程。比如在上面二分查找中就是 `return Math.max(leftMax, rightMax)`



根据a、b、d的不同情况进行不同的时间复杂度估算结果如下：

```tex
log(b,a)>d	时间复杂度为O(N^log(b,a))
log(b,a)=d	时间复杂度为O(N^d*logN)
log(b,a)<d	时间复杂度为O(N^d)
```



#### 用Master公式分析时间复杂度

1）用Master公式计算上面`二分查找最大值`的时间复杂度公式为：`T(N)=2*T(N/2)+O(1^0)`

> 根据这个公式可知：a=2，b=2，d=0



2）计算结果（对应公式 `log(b,a)>d	时间复杂度为O(N^log(b,a))`）：

```tex
# log(2,2)>0
log(b,a)>d

# O(N^log(2,2))
O(N^log(b,a))	# 结果为 O(N^1)

所以时间复杂度为：O(N)
```

