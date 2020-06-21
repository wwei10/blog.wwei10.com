---
layout: post
title:  "Fisher-Yates Shuffle"
date:   2014-02-22 22:22:00
categories: Chinese Programming
---

Last week, I was asked about random permutation in an interview:

```
given a sequence，design an algorithm to return a random
permutation. Each permutation should have equal chance of being returned.
```

I gave my solution and was asked to prove my algorithm was correct. However during my proof, I found my algorithm was wrong. This was my first time doing a mathematical proof in an interview.

At home, I found the solution to this problem (Fisher-Yates Shuffle)

```
To shuffle an array A of n elements (indices 0 .. n-1):
    for i from 0 to n - 2 do
        j <- random integer with i <= j <= n-1
        exchange a[j] and a[i]
```

We can easily prove that robability of i at position 0: `1 / n`.

To prove probability of i at position 1 is `1 / n`. We have two cases.

```
P[i at position 1]
= P[i not selected in 0] * P[i switched with 1]
= (n - 1) / n * 1 / (n - 1)
= 1 / n
```

We can generalize this further to prove `P[i at position j] = 1 / n`.
