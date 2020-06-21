---
layout: post
title:  "Fisher-Yates Shuffle"
date:   2014-02-22 22:22:00
categories: Chinese Programming
---

上个星期面试被问了一道random permutation的题目：

```
给定一个序列，设计一个算法返回一个random
permutation，要求每个permutation被返回的概率是一样的。
```

当时我就不知所措，答了一种错误的算法，面试官让我证明我的算法的正确性，我推导了下，发现自己的算法是错误的。这是我第一次被要求在面试时做数学证明。

回来后，我发现了Fisher-Yates shuffle这个算法：

```
To shuffle an array A of n elements (indices 0 .. n-1):
    for i from 0 to n - 2 do
        j <- random integer with i <= j <= n-1
        exchange a[j] and a[i]
```

为什么这个算法能保证每个permutation被返回的概率都是一样的呢？

如果经过Fisher-Yates算法，A[i]停留在i这个位置上的概率是1/n，我们就可以保证每个permutation被返回的概率是一样的。

最简单的情况，对于A[0]，A[0]还留在位置0的概率是1/n。

对于A[1]，A[1]留在位置1的发生的情况的条件是A[0]没有和A[1]交换，且A[1]和自己交换，这个事件发生的概率是```(n - 1) / n * 1 / (n - 1) = 1 / n```。

同理，对于之后的所有元素A[i]留在i位置的概率都是```1 / n```。
