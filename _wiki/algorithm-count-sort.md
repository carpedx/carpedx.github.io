---
layout: wiki
title: 计数排序
cate1: algorithm
cate2: 
description: 计数排序是一种非基于比较的排序算法，于1954年由Harold H. Seward提出。它在处理一定范围内的整数排序时，具有复杂度为Ο(n+k)的优势（其中k是整数的范围），快于任何比较排序算法。但是，这是一种牺牲空间换取时间的做法，而且在O(k)>O(n*log(n))的时候其效率反而不如基于比较的排序。
keywords: algorithm
sorting: 13
---



**计数排序是一种非基于比较的排序算法，于1954年由Harold H. Seward提出。它在处理一定范围内的整数排序时，具有复杂度为Ο(n+k)的优势（其中k是整数的范围），快于任何比较排序算法。但是，这是一种牺牲空间换取时间的做法，而且在O(k)>O(n*log(n))的时候其效率反而不如基于比较的排序。**

------



#### 基本思想

对每一个输入元素x，确定出小于x的元素个数。这样，我们就可以直接把x放到它在输出数组中的位置上了。

这个算法需要一个额外的数组来记录计数，所以如果范围非常大的话，可能需要较大的空间。



#### 算法思想

<img src="/images/wiki/algorithm/algorithm-count-sort_step1.jpg" />

其实会发现一个主要问题，那么就是tmp数组到底应该开辟多大的空间，怎么计算？

假设我们有组数据：`100, 101, 102, 101, 109, 105, 105`

我们应该开辟多大的空间，因为最大值是109，从0开始需要开辟110个空间吗？那太浪费了。

<img src="/images/wiki/algorithm/algorithm-count-sort_step2.jpg" />

所以我们只需要 `最大值 — 最小值 + 1` 的空间，`max=109, min=100, max-min+1=10` ，即可

<img src="/images/wiki/algorithm/algorithm-count-sort_step3.jpg" />



#### 代码流程图

<img src="/images/wiki/algorithm/algorithm-count-sort_step4.png" />



#### java


```java
public static void countSort(int[] a) {
    // 初始化数组中的最大值和最小值
    int max = a[0];
    int min = a[0];
    // 遍历数组，找到最大值和最小值
    for (int i = 0; i < a.length; i++) {
        if (a[i] > max) {
            max = a[i];
        }
        if (a[i] < min) {
            min = a[i];
        }
    }
    // 计算整数范围，为count数组的大小
    int range = max + 1 - min;
    // 初始化计数数组
    int[] count = new int[range];
    // 遍历原数组，计算每个数出现的次数并保存到count数组中
    for (int i = 0; i < a.length; i++) {
        count[a[i] - min]++;
    }
    // 对count数组进行累加
    for (int i = 1; i < range; i++) {
        count[i] += count[i - 1];
    }
    // 初始化一个与原数组等规模的辅助数组
    int[] tmp = new int[a.length];
    // 从原数组最后一个元素开始，将每个元素放到临时数组中对应的位置，并更新count数组
    for (int i = a.length - 1; i >= 0; i--) {
        tmp[count[a[i] - min] - 1] = a[i];
        count[a[i] - min]--;
    }
    // 将临时数组的元素复制回原数组，完成排序
    for (int i = 0; i < a.length; i++) {
        a[i] = tmp[i];
    }
}
```

