---
title: LeetCode-51-N皇后
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-05 14:32:10
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

#### [51. N 皇后](https://leetcode-cn.com/problems/n-queens/)

```java
class Solution {
   /**
     * 优化方法 -- check 同一列、主对角线、副对角线的状态检查
     * 主对角线 -- 横坐标 - 纵坐标 （在同一主对角线上的点，两坐标相减总相等 ，为了保证数组不越界，加上偏移量）
     * 副对角线 -- 横坐标 + 纵坐标 （在同一副对角线上的点，两坐标相加总相等）
     */

    /**
     * 回溯算法（按行存放 皇后 ，backTracing(help, 0);）
     * backTracing(String[][] help, int index) 当index大于等于n时，结束递归，加入结果集
     * <p>
     * 当准备向 第 index 行 选择第 j 列，进行存放 Q 时，先进行竖向检查、左上检查、以及右上检查，都符合条件时，再修改数组，进行递归，
     * 最后 修改回 原先临时数组状态
     *
     * @param n
     * @return
     */

    static List<List<String>> result;

    static boolean[] shuXiangHelp;//j为坐标  size = length  i - j 的取值范围为：[-(length-1) , length -1]
    static boolean[] zhuDuiJiaoHelp;// i -j + length -1为坐标  size = 2*length-1;
    static boolean[] fuDuiJiaoHelp;// i -j + length -1为坐标  size = 2*length-1;


    public static List<List<String>> solveNQueens(int n) {
        char[][] help = new char[n][n];
        shuXiangHelp = new boolean[n];
        zhuDuiJiaoHelp = new boolean[2 * n - 1];
        fuDuiJiaoHelp = new boolean[2 * n - 1];
        result = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            Arrays.fill(help[i], '.');
        }
        backTracing(help, 0);
        return result;
    }

    private static void backTracing(char[][] help, int index) {
        //backTracing(String[][] help, int index) 当index大于等于n时，结束递归，加入结果集
        if (index >= help.length) {
            ArrayList<String> list = new ArrayList<>();
            for (int i = 0; i < help.length; i++) {
                StringBuilder builder = new StringBuilder();
                for (int j = 0; j < help[i].length; j++) {
                    builder.append(help[i][j]);
                }
                list.add(builder.toString());
            }
            result.add(list);
            return;
        }
        // 当准备向 第 index 行 选择第 j 列，进行存放 Q 时，先进行竖向检查、左上检查、以及右上检查，都符合条件时，再修改数组，进行递归，
        // 最后 修改回 原先临时数组状态
        for (int j = 0; j < help.length; j++) {
            if (!checkZhuDuiJiao(index, j, help.length)) {
                continue;
            }
            if (!checkFuDuiJiao(index, j, help.length)) {
                continue;
            }
            if (!checkShuxiang(j)) {
                continue;
            }
            zhuDuiJiaoHelp[index - j + help.length - 1] = true;
            fuDuiJiaoHelp[index + j] = true;
            shuXiangHelp[j] = true;
            help[index][j] = 'Q';
            backTracing(help, index + 1);
            help[index][j] = '.';
            zhuDuiJiaoHelp[index - j + help.length - 1] = false;
            fuDuiJiaoHelp[index + j] = false;
            shuXiangHelp[j] = false;
        }
    }

    /**
     * 主对角线 -- 横坐标 - 纵坐标 （在同一主对角线上的点，两坐标相减总相等 ，为了保证数组不越界，加上偏移量）
     *
     * @param index
     * @param j
     * @param length
     * @return
     */
    private static boolean checkZhuDuiJiao(int index, int j, int length) {
        return !zhuDuiJiaoHelp[index - j + length - 1];
    }

    /**
     * 副对角线 -- 横坐标 + 纵坐标 （在同一副对角线上的点，两坐标相加总相等）
     *
     * @param index
     * @param j
     * @param length
     * @return
     */
    private static boolean checkFuDuiJiao(int index, int j, int length) {
        return !fuDuiJiaoHelp[index + j];
    }

    private static boolean checkShuxiang(int j) {
        return !shuXiangHelp[j];
    }


    private static boolean checkXiexiang1(char[][] help, int index, int j) {
        while (index - 1 >= 0 && j - 1 >= 0) {
            if (help[index - 1][j - 1] == 'Q') {
                return false;
            }
            index--;
            j--;
        }
        return true;
    }

    private static boolean checkXiexiang2(char[][] help, int index, int j) {
        while (index - 1 >= 0 && j + 1 < help.length) {
            if (help[index - 1][j + 1] == 'Q') {
                return false;
            }
            index--;
            j++;
        }
        return true;
    }

    private static boolean checkShuxiang2(char[][] help, int index, int j) {
        for (int i = 0; i <= index; i++) {
            if (help[i][j] == 'Q') {
                return false;
            }
        }
        return true;
    }
}
```

