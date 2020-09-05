---
title: LeetCode-459-重复的子字符串
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-24 23:34:21
password:
summary:
tags: [KMP,字符串匹配]
categories: 
- LeetCode
- KMP模式匹配

---

#### [459. 重复的子字符串](https://leetcode-cn.com/problems/repeated-substring-pattern/)

```java
class Solution {
/**
     * 当 s 没有循环节时：
     * 如果 s 中没有循环节，那么 ss 中必然有且只有两个 s，此时从 ss[1] 处开始寻找 s ，必然只能找到第二个，所以此时返回值为 s.size()。
     * ----------------
     * 当 s 有循环节时：
     * 当 s 中有循环节时，设循环节为 r，其长度为 l，那么 ss 中必然可以找出 s.size()/l + 1 个 s 。
     * 因为去掉了第一个 S 的第一个字符 (代码中，(s+s).find(s, 1)， 是从 ss[1] 处开始 find )
     * 所以此时必回找到第二个 s 的起点。
     *
     * @param s
     * @return
     */
    public static boolean repeatedSubstringPattern(String s) {
        String substring = (s + s).substring(1, (s + s).length());
        return substring.indexOf(s) != (s.length() - 1);
    }

    /**
     * 模拟
     * 如果匹配，则至少循环两次，所以模板子串的最长长度为 length / 2;
     * 从 length / 2 遍历到 1；如果不能被 length 整除，则直接continue，否则遍历N的次数，stringbuilder append 去判断是否相等
     *
     * @param s
     * @return
     */
    public static boolean repeatedSubstringPattern2(String s) {
        int length = s.length();
        int count = length / 2;
        boolean flag = false;
        for (int i = count; i >= 1; i--) {
            if (length % i == 0) {
                if (deal(s, i)) {
                    flag = true;
                    break;
                }
            }
        }
        return flag;
    }

    public static boolean deal(String s, int length) {
        StringBuilder sb = new StringBuilder();
        int count = s.length() / length;
        String temp = s.substring(0, length);
        for (int i = 0; i < count; i++) {
            sb.append(temp);
        }
        return temp.toString().equals(s);
    }
}
```

<!-- more -->

## 模式匹配解法

```java
// TODO 

```

