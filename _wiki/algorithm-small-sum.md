---
layout: wiki
title: 求小和问题
cate1: algorithm
cate2: 
description: 求小和问题
keywords: algorithm
---



**在一个数组中，每一个数左边比当前数小的数累加起来，叫做这个数组的小和。求一个数组的小和？**

------

```tex
例子：[1, 3, 4, 2, 5] 
1左边比1小的数，没有
3左边比3小的数，1
4左边比4小的数，1、3
2左边比2小的数，1
5左边比5小的数，1，3，4，2
所以小和为 1+1+3+1+1+3+4+2=16
```



#### 思路分析

**第一种思路**

暴力递归

通过一个指针，然后遍历去看左边有多少个数比他小，然后进行相加，最终的得到结果。
这种方法的时间复杂度为O(N^2)

**第二种思路**

通过归并排序在进行排序的同时拿到他的小和

可以转换思路，题目的要求是要找打左边数比该数小的数字将加，那么我们可以转变为看看右边有多少个数字比他大（假设为n），表示该数在小和的计算中出现了n次，最后相加即可。
即右边比1大的数有4个，比3大的数有2个，比4大的数有1个，比2大的数有1个。小和为：`1×4+3×2+4×1+2×1=16`

另外判断右边比左边大的数有多少个，是通过排序然后计算数组下标数量得到的



#### 流程图

<img src="/images/wiki/algorithm/algorithm-small-sum_step1.png"  />



#### java

```java
public static void main(String[] args) {
    // 定义一个整型数组  
    int[] arr = {1, 3, 4, 2, 5};
    // 调用 smallSum 方法并打印结果
    System.out.println(smallSum(arr));
}

/**  
 * 计算数组中小和的方法  
 * @param arr 整型数组  
 * @return 返回小和的结果  
 */ 
public static int smallSum(int[] arr) {
    // 如果数组为空或长度小于2，返回0  
    if (arr == null || arr.length < 2) return 0;

    // 调用 process 方法并返回结果 
    return process(arr, 0, arr.length - 1);
}

/**  
 * 分治算法的核心方法，计算数组中小和的过程  
 * @param arr 整型数组  
 * @param L 数组的左边界  
 * @param R 数组的右边界  
 * @return 返回小和的结果  
 */  
public static int process(int[] arr, int L, int R) {
    // 如果左右边界相等，返回0
    if (L == R) return 0;

    // 计算中间位置
    int mid = L + ((R - L) >> 1);
    // 递归计算左半部分的小和
    int leftSum = process(arr, L, mid);
	// 递归计算右半部分的小和
    int rightSum = process(arr, mid + 1, R);
    // 计算合并后的小和
    int mergeSum = merge(arr, L, mid, R);
    // 返回三部分小和的总和 
    return leftSum + rightSum + mergeSum;
}

/**  
 * 合并两个有序数组并计算小和的方法  
 * @param arr 整型数组  
 * @param L 数组的左边界  
 * @param M 数组的中间位置  
 * @param R 数组的右边界  
 * @return 返回小和的结果  
 */ 
public static int merge(int[] arr, int L, int M, int R) {
    // 创建一个临时数组用于存放合并后的结果 
    int[] help = new int[R - L + 1];
    int i = 0;
    // 定义两个指针分别指向左右两个有序数组的起始位置 
    int p1 = L;
    int p2 = M + 1;
    int res = 0;
    // 循环比较左右部分
    while (p1 <= M && p2 <= R) {
        // 左半部分比右半部分小 产生小和
        // (R - p2 + 1) 判断右半部分有多少个比 arr[p1] 大的数，个数乘以 arr[p1] 得到小和
        res += arr[p1] < arr[p2] ? (R - p2 + 1) * arr[p1] : 0;
        // 进行排序 如果左半部分当前下标值小，就将值放入临时数组然后下标+1，否则右半部分下标+1
        help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
    }
    // 将左半部分剩余的元素放入临时数组
    while (p1 <= M) {
        help[i++] = arr[p1++];
    }
    // 将右半部分剩余的元素放入临时数组
    while (p2 <= R) {
        help[i++] = arr[p2++];
    }
    // 将临时数组中的元素复制回原数组对应的位置
    for (i = 0; i < help.length; i++) {
        arr[L + i] = help[i];
    }
    // 返回小和的结果 
    return res;
}
```

