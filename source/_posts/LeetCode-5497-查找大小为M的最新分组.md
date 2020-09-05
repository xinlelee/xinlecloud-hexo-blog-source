---
title: LeetCode-5497-查找大小为M的最新分组
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-23 22:15:54
password:
summary:
tags: [并查集]
categories:
- LeetCode
- 并查集
---

#### [5497. 查找大小为 M 的最新分组](https://leetcode-cn.com/problems/find-latest-group-of-size-m/)

```java
public class 查找大小为M的最新分组_5497 {

    int[] parent;
    int[] nums;

    /**
     * 并查集 -- 使用两个数组，一个保存当前节点的父节点，另一个用来保存集合父节点所在集合的总的个数
     * 之后就是遍历数组，每个节点都要判断 左右节点的情况，一共四种
     * 使用int count 计数，符合 m 的个数的集合 一种的个数，处理当前节点时，判断count 是否为0
     * 不为0，则表明当前步骤有有效步骤，赋值给 result;否则，当前步骤无效，不做记录
     * @param arr
     * @param m
     * @return
     */
    public int findLatestStep(int[] arr, int m) {
        int result = -1;
        int length = arr.length;
        int count = 0;
        //数组的范围 取值为 0-1--n-n+1,为了防止数组越界，其中0 和 n+1 防止数组越界
        parent = new int[length + 2];
        nums = new int[length + 2];
        for (int i = 1; i <= length; i++) {
            int index = arr[i - 1];
            //如果，当前位置的左右两个数都为0 (不会对当前的数字情况造成任何影响)
            if (parent[index - 1] == 0 && parent[index + 1] == 0) {
                parent[index] = index;
                nums[index] = 1;
                if (nums[index] == m) {
                    count++;
                }
            }
            //当前位置的 左边不为 0 ，右边为 0
            else if (parent[index - 1] != 0 && parent[index + 1] == 0) {
                //注意合并集的时候，往右合并（较大值）--这样 左边的数 就无需递归寻找父节点
                parent[index - 1] = index;
                parent[index] = index;
                nums[index] = 1 + nums[index - 1];
                //
                if (nums[index - 1] == m) {
                    count--;
                }
                if (nums[index] == m) {
                    count++;
                }
            }
            //当前位置的 左边为 0 ，右边不为 0
            else if (parent[index - 1] == 0 && parent[index + 1] != 0) {
                int parentNode = findParent(index + 1);
                parent[index] = parentNode;
                if (nums[parentNode] == m) {
                    count--;
                }
                nums[parentNode] = 1 + nums[parentNode];
                //
                if (nums[parentNode] == m) {
                    count++;
                }
            }
            //当前位置的 左边 和 右边 都不为 0
            else {
                int parentNode = findParent(index + 1);
                parent[index] = parentNode;
                parent[index - 1] = parentNode;
                if (nums[parentNode] == m) {
                    count--;
                }
                if (nums[index - 1] == m) {
                    count--;
                }
                nums[parentNode] = 1 + nums[parentNode] + nums[index - 1];
                if (nums[parentNode] == m) {
                    count++;
                }
            }

            // 当前节点处理完毕，判断count，不为0，则表明当前步骤有有效步骤，赋值给 result;
            if (count != 0) {
                result = i;
            }
        }
        return result;
    }

    /**
     * 递归寻找父节点
     * @param index
     * @return
     */
    private int findParent(int index) {
        if (parent[index] != index) {
            return findParent(parent[index]);
        }
        return index;
    }
```

