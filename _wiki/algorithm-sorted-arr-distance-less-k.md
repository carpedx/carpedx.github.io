---
layout: wiki
title: 对一个几乎有序的数组排序
cate1: algorithm
cate2: 
description: 已知一个几乎有序的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动的距离可以不超过K，并且K相对于数组来说比较小。请选择一个合适的排序算法针对这个数组进行排序。
keywords: algorithm
sorting: 12
---



**已知一个几乎有序的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动的距离可以不超过K，并且K相对于数组来说比较小。请选择一个合适的排序算法针对这个数组进行排序。**

------




#### 思路分析

假设 `K=6`，数组为：`[10, 2, 3, 5, 1, 15, 4, 20, 7, 30]`

已知在这个数组中，每个元素移动距离不超过6就能排好顺序，那么数组前6个中的最小值一定是整个数组中的最小值，因为超过的数字移动6次也不会移动到下标0的位置

那么可以进行如下操作：

```
1）先将前K个数组中的值放入小根堆
2）将堆顶元素（最小元素）弹出，然后陆续将K后面的添加进来，将堆顶元素（最小元素）弹出
```



#### 流程图

<img src="/images/wiki/algorithm/algorithm-sorted-arr-distance-less-k_step1.png"  />



#### java

```java
public static void main(String[] args) {
    // 定义一个几乎有序的数组
    int[] arr = {10, 2, 3, 5, 1, 15, 4, 20, 7, 30};
    // 定义每个元素移动的距离不超过K，这里的K是6
    int K = 6;
    // 调用方法对数组进行排序
    sortedArrDistanceLessK(arr, K);
    // 输出排序后的数组
    System.out.println(Arrays.toString(arr));
}

public static void sortedArrDistanceLessK(int[] arr, int K) {
    // 创建一个优先级队列（小根堆）
    // 默认小根堆，堆顶元素最小
    PriorityQueue<Integer> heap = new PriorityQueue<>();
    // 将数组的前K个元素添加到堆中
    int index = 0;
    for (; index <= Math.min(arr.length, K); index++) {
        heap.add(arr[index]);
    }
    // 开始处理数组的后续元素
    for (; index < arr.length; i++, index++) {
        // 将新元素添加到堆中
        heap.add(arr[index]);
        // 将堆顶元素（最小元素）弹出
        arr[i] = heap.poll();
    }
    // 处理完所有元素后，堆中可能还有剩余元素
    while (!heap.isEmpty()) {
        // 将堆顶元素（最小元素）取出并赋值给数组的当前位置
        arr[i++] = heap.poll();
    }
}
```



#### 补充

PriorityQueue默认小根堆，要将其转为大根堆只须使用比较器：

```java
public static class AComp implements Comparator<Integer> {
    // 如果返回负数，认为第一个参数应该排在前面
    // 如果返回正数，认为第二个参数应该排在前面
    // 如果返回0，认为谁放前面都行
    @Override
    public int compare(Integer arr0, Integer arr1) {
        return arr1 - arr0;
    }
}

// 变为大根堆
PriorityQueue<Integer> heap = new PriorityQueue<>(new AComp());
```

