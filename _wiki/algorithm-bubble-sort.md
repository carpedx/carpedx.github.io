---
layout: wiki
title: 冒泡排序
cate1: algorithm
cate2: 
description: 冒泡排序
keywords: algorithm
---



**冒泡排序：重复地走访过要排序的元素列，依次比较两个相邻的元素，如果顺序（如从大到小、首字母从Z到A）错误就把他们交换过来**

------



**动图演示**

<img src="/images/wiki/algorithm/algorithm-bubble-sort_step1.gif"  />



**java**

```java
public static void bubbleSort(int[] arr) {
    // 如果数组为空或长度小于2，则直接返回，无需排序
    if (arr == null || arr.length < 2) return;

    // 外层循环，从数组长度减1开始遍历到1，表示每次循环都会将最大的元素冒泡到最后
    for (int i = arr.length - 1; i > 0; i--) {
        // 内层循环，从第一个元素开始遍历到当前最大元素的前一个位置  
        // 进行相邻元素的比较和交换  
        for (int j = 0; j < i; j++) {
            // 如果当前元素比后一个元素大，则交换它们的位置 
            if (arr[j] > arr[j + 1]) {
                swap(arr, j, j + 1);
            }
        }
    }
}

// 交换数组中两个元素的位置，使用异或运算实现交换
public static void swap(int[] arr, int i, int j) {
    arr[i] = arr[i] ^ arr[j];
    arr[j] = arr[i] ^ arr[j];
    arr[i] = arr[i] ^ arr[j];
}
```

