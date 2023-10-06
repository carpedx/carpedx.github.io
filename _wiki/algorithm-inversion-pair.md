---
layout: wiki
title: 逆序对问题
cate1: algorithm
cate2: 
description: 在一个数组中，左边的数如果比右边的数大，则这两个数构成一个逆序对，请找到逆序对的数量？
keywords: algorithm
sorting: 7
---



**逆序对问题：在一个数组中，左边的数如果比右边的数大，则这两个数构成一个逆序对，请找到逆序对的数量？**

------


```tex
例子：[3, 2, 4, 5, 0] 
对于3来说逆序对：3-2和3-0
对于2来说逆序对：2-0
对于4来说逆序对：4-0
对于5来说逆序对：5-0
总共5个逆序对
```



#### 思路分析

- 可以转换思路，题目的要求是要找左边比右边数大，那么我们可以转变为看看右边有多少个数字比左边小
- 即右边比3小的数有2，0，比2小的数有0，比4小的数有0，比5小的数有0，逆序数量为5
- 另外判断右边比左边大的数有多少个，是通过排序然后计算数组下标数量得到的



#### 流程图

<img src="/images/wiki/algorithm/algorithm-inversion-pair_step1.png"  />



#### java

> 相关代码：[求小和问题](https://carpedx.com/wiki/algorithm-small-sum/)

```java
public static void main(String[] args) {
    int[] arr = {3, 2, 4, 5, 0};
    System.out.println(inversionPairs(arr));
}

public static int inversionPairs(int[] arr) {
    if (arr == null || arr.length < 2) return 0;

    return process(arr, 0, arr.length - 1);
}

public static int process(int[] arr, int L, int R) {
    if (L == R) return 0;

    int mid = L + ((R - L) >> 1);
    int leftSum = process(arr, L, mid);
    int rightSum = process(arr, mid + 1, R);
    int mergeSum = merge(arr, L, mid, R);
    return leftSum + rightSum + mergeSum;
}

public static int merge(int[] arr, int L, int M, int R) {
    int[] help = new int[R - L + 1];
    int i = 0;
    int p1 = L;
    int p2 = M + 1;
    int res = 0;
    while (p1 <= M && p2 <= R) {
        // 与求小和问题的区别是此处p1大于p2下标时，逆序对数量+1即可
        res += arr[p1] > arr[p2] ? 1 : 0;
        // 与求小和问题的区别是将此处改为了倒序
        help[i++] = arr[p1] > arr[p2] ? arr[p1++] : arr[p2++];
    }
    while (p1 <= M) {
        help[i++] = arr[p1++];
    }
    while (p2 <= R) {
        help[i++] = arr[p2++];
    }
    for (i = 0; i < help.length; i++) {
        arr[L + i] = help[i];
    }
    return res;
}
```

