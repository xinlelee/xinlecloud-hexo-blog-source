---
title: LeetCode-486-预测赢家
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-05 14:27:53
password:
summary:
tags: [递归,动态规划]
categories:
- LeetCode
- 动态规划
Java:
LeetCode:
---

#### [486. 预测赢家](https://leetcode-cn.com/problems/predict-the-winner/)

```java
class Solution {
/**
     * 递归版本 2
     *
     * @param nums
     * @return
     */
    public static boolean PredictTheWinner(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        int n = dfs(nums, 0, nums.length - 1);
        return n >= (sum - n);
    }

    //表示，先手时，在begin - end 范围内能取的最大值
    private static int dfs(int[] nums, int begin, int end) {
        if (begin == end) {
            return nums[begin];
        }
        if (begin + 1 == end) {
            return Math.max(nums[begin], nums[end]);
        }
        // 当前选择begin位置上的数时，则下次选择的范围只能是[begin+1,end-1](后手选择了end)、或者是[bengin+2,end](后手选择了begin+1)
        // Math.min(dfs(nums, begin + 1, end - 1), dfs(nums, begin + 2, end));后手一定会留最小的取值范围
        int beginValue = nums[begin] + Math.min(dfs(nums, begin + 1, end - 1), dfs(nums, begin + 2, end));
        int endValue = nums[end] + Math.min(dfs(nums, begin + 1, end - 1), dfs(nums, begin, end - 2));
        return Math.max(beginValue, endValue);
    }


    /**
     * 递归版本一
     *
     * @param nums
     * @return
     */
    public static boolean PredictTheWinner4(int[] nums) {
        return dfs2(nums, 0, nums.length - 1, 1) >= 0;
    }

    private static int dfs2(int[] nums, int begin, int end, int flag) {
        if (begin == end) {
            return nums[begin] * flag;
        }
        int socreBegin = nums[begin] * flag + dfs2(nums, begin + 1, end, -flag);
        int socreEnd = nums[end] * flag + dfs2(nums, begin, end - 1, -flag);
        if (flag == 1) {
            return Math.max(socreBegin, socreEnd);
        } else {
            return Math.min(socreBegin, socreEnd);
        }
    }


    /**
     * 优化的 DP  -- dp[i][j] 与 另一方法的 表意不同
     * 定义二维数组 dp，其行数和列数都等于数组的长度，dp[i][j] 表示当数组剩下的部分为下标 i 到下标 j时，当前玩家领先数值大小
     * 当前玩家与另一个玩家的分数之差的最大值，注意当前玩家不一定是先手。
     * <p>
     * 只有当 i≤j 时，数组剩下的部分才有意义，因此当 i>j 时，dp[i][j]=0。
     *
     * @param nums
     * @return
     */
    public static boolean PredictTheWinner2(int[] nums) {
        int length = nums.length;
        int[][] dp = new int[length][length];
        for (int i = 0; i < length; i++) {
            dp[i][i] = nums[i];
        }
        for (int end = 1; end < length; end++) {
            for (int begin = end - 1; begin >= 0; begin--) {
                //begin -end 范围内，player1先手，取begin位置上的数， nums[begin] - dp[begin + 1][end]
                //dp[begin + 1][end] 表示 此时 player2 的领先数值大小
                dp[begin][end] = Math.max(nums[begin] - dp[begin + 1][end], nums[end] - dp[begin][end - 1]);
            }
        }
        return dp[0][length - 1] >= 0;
    }

    static int[] temp;

    /**
     * dp 动态规划  int[][] dp = new int[length][length];
     * dp[begin][end] 表示: 在nums数组中的begin - end 范围内，先手 所能获取到的最大值
     * 就是计算出player1可能得到的最大分数，然后用数组总和减去player1的得分就是player2的得分，然后两者比较一下就可以了。
     *
     * @param nums
     * @return
     */
    public static boolean PredictTheWinner3(int[] nums) {
        int length = nums.length;
        int[][] dp = new int[length][length];
        temp = new int[length];
        int pre = 0;
        for (int i = 0; i < length; i++) {
            temp[i] = pre + nums[i];
            pre = temp[i];
        }
        for (int end = 0; end < length; end++) {
            for (int begin = end; begin >= 0; begin--) {
                if (begin == end) {
                    // 如果当前 begin 和 end 相等，则dp[begin][end] = nums[end];
                    dp[begin][end] = nums[end];
                } else {
                    //当前先手取 begin位置上的值，则 num1 = nums[begin] + （begin-1,end）范围内的最小值
                    //（begin-1,end）范围内的最小值 的获取： sum(begin + 1, end, nums) - dp[begin + 1][end];
                    //（begin-1,end）所有数据之和  减去 （begin-1,end）范围内先手的最大值
                    int num1 = nums[begin] + sum(begin + 1, end, nums) - dp[begin + 1][end];
                    //当前先手取 end 位置上的值
                    int num2 = nums[end] + sum(begin, end - 1, nums) - dp[begin][end - 1];
                    dp[begin][end] = Math.max(num1, num2);
                }
            }
        }
        int sum = sum(0, length - 1, nums);
        int leftNum = sum - dp[0][length - 1];
        return dp[0][length - 1] >= leftNum;
    }

    //求取begin - end 之间的和
    private static int sum(int begin, int end, int[] nums) {
        return temp[end] - temp[begin] + nums[begin];
    }
}
```

