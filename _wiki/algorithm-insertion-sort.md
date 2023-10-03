---
layout: wiki
title: 插入排序
cate1: algorithm
cate2: 
description: 插入排序
keywords: algorithm
---



**插入排序：插入排序的工作方式像许多人排序一手扑克牌。开始时，我们的左手为空并且桌子上的牌面向下。然后，我们每次从桌子上拿走一张牌并将它插入左手中正确的位置。为了找到一张牌的正确位置，我们从右到左将它与已在手中的每张牌进行比较，直到前一张牌比它还小时插入。**

------



> 插入排序的时间复杂度在最坏情况下为O(n^2)，但在最好情况下为O(n)
>
> 与冒泡排序和选择排序相比，插入排序在部分有序的数据上表现较好



**动图演示**

<img src="/images/wiki/algorithm/algorithm-insertion-sort_step1.gif"  />



```java
public static void insertionSort(int[] arr) {
    // 如果数组为空或长度小于2，则直接返回，无需排序 
    if (arr == null || arr.length < 2) return;

    // 外层循环，从第二个元素开始遍历到最后一个元素  
    // 每一次循环都将当前元素插入到已排序序列的正确位置
    for (int i = 1; i < arr.length; i++) {
        // 内层循环，从当前元素的前一个位置开始向前遍历  
        // 如果已排序元素大于当前元素，则交换它们的位置  
        // 直到找到已排序元素小于或等于当前元素的位置，将当前元素插入到该位置后
        for (int j = i - 1; j >= 0 && arr[j] > arr[j + 1]; j--) {
            swap(arr, j, j + 1);
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

