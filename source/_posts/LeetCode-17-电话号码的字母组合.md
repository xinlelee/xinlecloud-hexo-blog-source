---
title: LeetCode-17-电话号码的字母组合
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-26 22:51:06
password:
summary:
tags: [DFS,回溯]
categories:
- LeetCode
- DFS
- 回溯
Java:
LeetCode:
---

#### [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

```java
class Solution {
    static String[][] dir = {{"a", "b", "c"}, {"d", "e", "f"}, {"g", "h", "i"},
            {"j", "k", "l"}, {"m", "n", "o"}, {"p", "q", "r", "s"}, {"t", "u", "v"},
            {"w", "x", "y", "z"}};
    static List<String> result;

    /**
     * dfs + 回溯
     *
     * @param digits
     * @return
     */
    public static List<String> letterCombinations(String digits) {
        result = new ArrayList<>();
        if (digits == null || digits.length() == 0) {
            return result;
        }
        StringBuilder builder = new StringBuilder();
        //传入初始参数
        dfs(digits, 0, builder);
        return result;
    }

    /**
     * dfs 回溯时，先判断结束条件，即 判断是否满足条件，将临时数据保存到结果集合中
     * 递归前，先修改，再递归， 最后恢复
     *
     * @param digits
     * @param index
     * @param builder
     */
    private static void dfs(String digits, int index, StringBuilder builder) {
        if (builder.length() == digits.length()) {
            result.add(builder.toString());
            return;
        }
        int i = (digits.charAt(index) - '0') - 2;
        String[] temp = dir[i];
        for (int j = 0; j < temp.length; j++) {
            builder.append(temp[j]);
            dfs(digits, index + 1, builder);
            builder.deleteCharAt(builder.length() - 1);
        }
    }
}
```

