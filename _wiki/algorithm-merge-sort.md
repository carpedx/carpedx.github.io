---
layout: wiki
title: 归并排序
cate1: algorithm
cate2: 
description: 归并排序
keywords: algorithm
---



**归并排序：直接将以数组的中心位置作为分界点，不断取中间位置，分成两个小区间，直到无法分解**

------



**思路流程**

先分解：

将数组分解至无法继续往下分解

<img src="/images/wiki/algorithm/algorithm-merge-sort_step1.jpg"  />

在排序：

排序时，从最底层开始排序

<img src="/images/wiki/algorithm/algorithm-merge-sort_step2.jpg"  />



**java**

```java
public static void main(String[] args) {
    // 初始化一个整型数组
    int[] arr = {9, 3, 4, 6, 2, 5, 7, 1, 8};
    // 调用归并排序函数，对数组进行排序
    mergeSort(arr);
    // 打印排序后的数组
    System.out.println(Arrays.toString(arr));
}

// 归并排序函数，接收一个整型数组作为参数，对其进行排序
public static void mergeSort(int[] arr) {
    // 如果数组为空或者只有一个元素，则直接返回，不需要排序
    if (arr == null || arr.length < 2) return;

    // 调用递归排序函数，传入数组和范围参数，对数组进行递归排序
    process(arr, 0, arr.length - 1);
}

// 递归排序函数，接收一个整型数组和两个整数L、R作为参数，表示对数组中[L...R]范围内的元素进行排序
public static void process(int[] arr, int L, int R) {
    // 如果范围只有一个元素，则直接返回，不需要排序
    if (L == R) return;

    // 计算范围的中间点索引，使用位运算加速计算
    int mid = L + ((R - L) >> 1);	// 中间点索引
    // 递归调用process函数，对左半部分[L...mid]进行排序
    process(arr, L, mid);
    // 递归调用process函数，对右半部分[mid+1...R]进行排序
    process(arr, mid + 1, R);
    // 调用合并函数，将左右两部分合并为一个有序序列
    merge(arr, L, mid, R);
}

// 合并函数，接收一个整型数组和三个整数L、M、R作为参数，表示将数组中[L...M]和[M+1...R]两个有序序列合并为一个有序序列
public static void merge(int[] arr, int L, int M, int R) {
    // 创建一个临时数组，用于存储合并后的有序序列
    int[] help = new int[R - L + 1];
    // 初始化临时数组的索引为0
    int i = 0;
    // 初始化第一个有序序列的索引为L
    int p1 = L;
    // 初始化第二个有序序列的索引为M+1
    int p2 = M + 1;
    // 循环比较两个有序序列的元素大小，将较小的元素存入临时数组中，直到其中一个序列被遍历完
    while (p1 <= M && p2 <= R) {
        help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
    }
    // 将第一个有序序列中剩余的元素存入临时数组中
    while (p1 <= M) {
        help[i++] = arr[p1++];
    }
    // 将第二个有序序列中剩余的元素存入临时数组中
    while (p2 <= R) {
        help[i++] = arr[p2++];
    }
    // 将临时数组中的元素复制回原数组中对应的位置，完成合并操作
    for (i = 0; i < help.length; i++) {
        arr[L + i] = help[i];
    }
}
```

