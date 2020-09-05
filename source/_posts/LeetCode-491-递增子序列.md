---
title: LeetCode-491-递增子序列
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-25 23:52:55
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

#### [491. 递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)

```java
class Solution {
    List<List<Integer>> result;

    public List<List<Integer>> findSubsequences(int[] nums) {
        // 定义全局变量保存结果
        result = new ArrayList<List<Integer>>();
        LinkedList<Integer> list = new LinkedList<>();
        // idx 初始化为 -1，开始 dfs 搜索。
        dfs(nums, -1, list);
        return result;
    }

    private void dfs(int[] nums, int index, LinkedList<Integer> list) {
        // 只要当前的递增序列长度大于 1，就加入到结果 res 中，然后继续搜索递增序列的下一个值。
        if (list.size() > 1) {
            result.add(new LinkedList(list));
        }
        // 在 [idx + 1, nums.length - 1] 范围内遍历搜索递增序列的下一个值。
        // 借助 set 对 [idx + 1, nums.length - 1] 范围内的数去重。
        // 注： 表明在当前搜索的范围内，寻找一位符合条件的值，若当前值已经搜索过，则直接continue,否则加入set中，注意哦，set不是dfs的参数
        // 仅仅保存当前搜索范围内的 已搜索情况
        HashSet<Integer> set = new HashSet<>();
        for (int i = index + 1; i < nums.length; i++) {
            int temp = nums[i];
            // 1. 如果 set 中已经有与 nums[i] 相同的值了，说明加上 nums[i] 后的所有可能的递增序列之前已经被搜过一遍了，因此停止继续搜索。
            if (set.contains(temp)) {
                continue;
            }
            set.add(temp);
            // 2. 如果 nums[i] >= list.getLast() 的话，说明出现了新的递增序列，因此继续 dfs 搜索
            // （因为 list 在这里是复用的，因此别忘了 remove 哦）
            if (list.isEmpty() || list.getLast() <= temp) {
                list.addLast(temp);
                dfs(nums, i, list);
                list.removeLast();
            }
        }
    }
}
```

