---
title: 字符串匹配算法-KMP
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-25 23:23:29
password:
summary:
tags: [KMP,字符串匹配算法]
categories:
- 算法
- 字符串匹配算法
Java:
LeetCode:
---

```java
    /**
     * * 第一步，求最大公共前后缀的长度
     * * 根据原始字符串新建一个等长的辅助数组 prefix，存储从第 0 位 开始 到 第 i 位的 parten 的最大公共前后缀 的长度
     * * <p>
     * * 0 --  AB
     * * 0 --  ABC
     * * 0 --  ABCD
     * * 1 --  ABCDA
     * * 2 -- ABCDAB
     * * 3 -- ABCDABC
     * * 1 -- ABCDABCA
     * * 最终 prefix数组为 [0, 0, 0, 0, 1, 2, 3, 1]
     * * <p>
     * * 第二步：开始匹配
     * * 当text[i] == parten[j]相等时，i++，j++,两个指针都右移
     * * 不相等时；j = prefix[j]; 若j==-1，说明已经到头，i++,j++
     *
     * @param text
     * @param parten
     * @return
     */
    public boolean kmpSearch(char[] text, char[] parten) {
        int partenLength = parten.length;
        int textLength = text.length;
        int[] prefix = new int[partenLength];
        //第一步：填充prefixs数组
        prefixTable(parten, prefix);
        //第二步：移动prefixs数组
        move_prefix_table(prefix);
        //双指针 ； i 指向 text的下标 -- j指向 parten的下标
        int i = 0;
        int j = 0;
        boolean flag = false;
        while (i < textLength) {
            // 当模式串的前partenLength - 1 都匹配，且 text当前位 等于 模式串的最后一位时，匹配成功
            if (j == partenLength - 1 && text[i] == parten[j]) {
                flag = true;
                result = i - partenLength + 1;
                break;
            }
            // 当text[i] == parten[j]相等时，i++，j++,两个指针都右移
            if (text[i] == parten[j]) {
                i++;
                j++;
            } else {
                j = prefix[j];
                //已经到头了，也都不相等, i++
                if (j == -1) {
                    i++;
                    j++;
                }
            }
        }
        return flag;
    }

    /**
     * 整体往右移动prefix数组，在第0位补为 -1；
     *
     * @param prefix
     */
    public void move_prefix_table(int[] prefix) {
        for (int i = prefix.length - 1; i > 0; i--) {
            prefix[i] = prefix[i - 1];
        }
        prefix[0] = -1;
    }


    /**
     * 使用双指针 j 和 i,初始化 j = 0 , i =1;
     * 遍历 i 到最后一位，i指明的是 当前处理的是 parten 的 第 i 位，且需要求出当前 公共前后缀数组的 第 i 位的值；
     * prefix[i]表示的意思是: 从第 0 位 开始 到 第 i 位的 parten 的最大公共前后缀 的长度
     * 初始默认 prefix[0] = 0;
     *
     * @param parten
     */
    public void prefixTable(char[] parten, int[] prefix) {
        int length = parten.length;
        prefix[0] = 0;
        int j = 0;
        int i = 1;
        while (i < length) {
            // 当 parten[i] == parten[j] 时，则 prefix[i] = j + 1;
            if (parten[i] == parten[j]) {
                prefix[i] = j + 1;
                i++;
                j++;
            } else {
                //如果 j == 0 ,则表明 已经到达头部节点 且 与 i 位置的数不相等，则可以直接将 prefix[i] 置为 0;
                if (j == 0) {
                    prefix[i] = 0;
                    i++;
                } else {
                    j = prefix[j - 1];
                }
            }
        }

    }
```

