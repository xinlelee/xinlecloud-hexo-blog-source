---
title: LeetCode-5496-你可以获得的最大硬币数目
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-23 20:19:55
password:
summary:
tags: [贪心算法]
categories:
- LeetCode
- 贪心算法
---

#### [5496. 你可以获得的最大硬币数目](https://leetcode-cn.com/problems/maximum-number-of-coins-you-can-get/)

```java
import java.util.Arrays;

public class 你可以获得的最大硬币数目_5496 {
    /**
     * 贪心算法
     * 先排序，再将当前最小的和最大的分给其他两人，自己保留次大的数，排除当前三个数后再依次执行
     *
     * @param piles
     * @return
     */
    public int maxCoins(int[] piles) {
        Arrays.sort(piles);
        int result = 0;
        int begin = piles.length / 3;
        //简化版： 先将最小的 N/3 个分出去，再从0开始第(N/3)个遍历累加，每次index+=2;
        for (; begin < piles.length; begin += 2) {
            result += piles[begin];
        }
        return result;
    }
}
```

