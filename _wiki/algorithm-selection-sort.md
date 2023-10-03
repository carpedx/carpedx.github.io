---
layout: wiki
title: 选择排序
cate1: algorithm
cate2: 
description: 选择排序
keywords: algorithm
---



**选择排序：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕**

------



> 与冒泡排序相比，选择排序的交换次数较少，但比较次数相同



**动图演示**

<img src="/images/wiki/algorithm/algorithm-selection-sort_step1.webp"  />



**java**

```java
public static void selectionSort(int[] arr) {
    // 如果数组为空或长度小于2，则直接返回，无需排序
    if (arr == null || arr.length < 2) return;

    // 外层循环，从第一个元素开始遍历到倒数第二个元素  
    // 每一次循环都将选出一个最小元素并将其交换到当前位置 
    for (int i = 0; i < arr.length - 1; i++) {
        // 初始化最小元素的索引为当前位置  
        int minIndex = i;
        
        // 内层循环，从当前位置的下一个元素开始遍历到最后一个元素  
        // 找出最小元素的索引
        for (int j = i + 1; j < arr.length; j++) {
            // 如果当前元素比最小元素小，则更新最小元素的索引 
            minIndex = arr[j] < arr[minIndex] ? j : minIndex;
        }
        
        // 交换当前位置和最小元素的位置 
        swap(arr, i, minIndex);
    }
}

// 交换数组中两个元素的位置
public static void swap(int[] arr, int i, int j) {
    int tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
}
```

