---
layout: wiki
title: 荷兰国旗问题
cate1: algorithm
cate2: 
description: 给定一个数组arr，和一个数num，请把小于num的数放在数组的左边，等于num的数放在数组的中间，大于num的数放在数组的右边
keywords: algorithm
---



**给定一个数组arr，和一个数num，请把小于num的数放在数组的左边，等于num的数放在数组的中间，大于num的数放在数组的右边**

------



#### 思路分析

首先它没要求左右两边有序，所以我们可以根据下面步骤实现：

- arr[i]<num，就将arr[i]和小于区下一个交换，小于区右扩
- arr==num，i++
- arr[i]>num，就将arr[i]和大于区前一个交换，大于区左扩，[i]原地不变



#### java

> 相关代码：[快速排序](https://carpedx.com/wiki/algorithm-quick-sort/)

```java
public static void main(String[] args) {
    int[] arr = {3, 5, 6, 3, 4, 5, 2, 6, 9, 0};
    int num = 3;	// 要求左边比3小，中间等于3，右边比3大
    dutchFlag(arr, num);
    System.out.println(Arrays.toString(arr));
}

public static void dutchFlag(int[] arr, int num) {
    if (arr == null || arr.length < 2) return;

    dutchFlag(arr, 0, arr.length - 1, num);
}

public static void dutchFlag(int[] arr, int L, int R, int X) {
    if (L >= R) return;
    
    // 不需要递归排序，因为题目没说左右要有顺序
	partition(arr, L, R, X);
}

public static void partition(int[] arr, int L, int R, int X) {
    int less = L - 1;
    int more = R;
    // 跟快排的主要区别是：
    // 	快排最后会将基准值和右区左边界值左交换，那么就可以做到左边区域小于等于基准值，右边区域大于基准值
    // 	那么数组就会是：【小于区(无序的)，等于区，大于区(无序的)】
    while (L <= more) {
        if (arr[L] < X) {
            swap(arr, ++less, L++);
        } else if (arr[L] > X) {
            swap(arr, more--, L);
        } else {
            L++;
        }
    }
}

public static void swap(int[] arr, int i, int j) {
    int tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
}
```

