---
title: LeetCode-5495-圆形赛道上经过次数最多的扇区
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-23 20:11:40
password:
summary:
tags: LeetCode
categories: LeetCode
---

#### [5495. 圆形赛道上经过次数最多的扇区](https://leetcode-cn.com/problems/most-visited-sector-in-a-circular-track/)

```java
class Solution {
/**
     * 1、使用N的数组保存，模拟计数
     * 2、简化问题：
     * （1）当数组的第一位 和 最后一位是相同的，则表明，中间跑了完整的 M 圈，
     * 由于是起始位置，那么就会相对于其他分段 就会多跑一次，所以返回起始阶段
     * （2）如果，起始点和结束点不一致，则分为两种情况，结束点 大于 起始点，则直接保存即可
     * 结束点 小于 起始点，表明跨越了最大分段，需要再从1加到结束点
     *
     * @param n
     * @param rounds
     * @return
     */
    public List<Integer> mostVisited(int n, int[] rounds) {
        List<Integer> result = new ArrayList<>();
        int begin = rounds[0];
        int end = rounds[rounds.length - 1];
        if (begin == end) {
            result.add(begin);
        } else {
            while (begin != end) {
                result.add(begin);
                begin++;
                if (begin > n) {
                    begin -= n;
                }
            }
            result.add(end);
        }
        Collections.sort(result);
        return result;
    }
}
```

