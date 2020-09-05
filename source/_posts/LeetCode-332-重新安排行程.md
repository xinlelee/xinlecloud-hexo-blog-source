---
title: LeetCode-332-重新安排行程
top: false
cover: false
toc: true
mathjax: true
date: 2020-08-27 23:54:12
password:
summary:
tags: [DFS,回溯,图,欧拉路径]
categories:
- LeetCode
- DFS
- 回溯-欧拉路径
Java:
LeetCode:
---

#### [332. 重新安排行程](https://leetcode-cn.com/problems/reconstruct-itinerary/)

```java
/**
 * tags: 欧拉路径  dfs 回溯  图
 * <p>
 * <p>
 * 什么是欧拉路径？欧拉路径就是一条能够不重不漏地经过图上的每一条边的路径，即小学奥数中的一笔画问题。而若这条路径的起点和终点相同，则将这条路径称为欧拉回路。
 * <p>
 * 如何判断一个图是否有欧拉路径呢？显然，与一笔画问题相同，一个图有欧拉路径需要以下几个条件：
 * <p>
 * 首先，这是一个连通图
 * 若是无向图，则这个图的度数为奇数的点的个数必须是0或2；若是有向图，则要么所有点的入度和出度相等，要么有且只有两个点的入度分别比出度大1和少1
 * 上面这两个条件很好证明。查找欧拉路径前，必须先保证该图满足以上两个条件，否则直接判误即可。
 * <p>
 * 查找欧拉路径的算法有Fluery算法和Hierholzer算法。下面介绍一下Hierholzer算法。
 *
 * @author l00511002
 * @since 2020-08-27
 */
class Solution {
    /**
     * 测试代码
     * 特殊用例--[["JFK","KUL"],["JFK","NRT"],["NRT","JFK"]]
     * String[][] array = {{"MUC", "LHR"}, {"JFK", "MUC"}, {"SFO", "SJC"}, {"LHR", "SFO"}};
     * String[][] array = {{"JFK", "KUL"}, {"JFK", "NRT"}, {"NRT", "JFK"}};
     * String[][] array = {{"JFK", "SFO"}, {"JFK", "ATL"}, {"SFO", "ATL"}, {"ATL", "JFK"}, {"ATL", "SFO"}};
     * List<List<String>> tickets = new ArrayList<>();
     * for (int i = 0; i < array.length; i++) {
     * String[] strs = array[i];
     * List<String> strings = Arrays.asList(strs);
     * tickets.add(strings);
     * }
     *
     * @param args
     */
    public static void main(String[] args) {
        String[][] array = {{"JFK", "KUL"}, {"JFK", "NRT"}, {"NRT", "JFK"}};
        List<List<String>> tickets = new ArrayList<>();
        for (int i = 0; i < array.length; i++) {
            String[] strs = array[i];
            List<String> strings = Arrays.asList(strs);
            tickets.add(strings);
        }
        System.out.println(findItinerary(tickets));
    }

    /**
     * 新建一个 HashMap<String, ArrayList<String>> map , key 表示 from , value 表示 to 的 list
     * map 填充完毕后，对 list 进行排序，目的在于保证找到result-size长度的第一个集合就是按字符自然排序的
     * （result的size一定等于tickets.length+1）
     * 接着 DFS + 回溯 ，这里注意，从list中获取一个to时，先判断是否为空串，若不是，加入到tempList中，继续调用dfs方法
     * 若是空串，表明，该 to 已经访问过，直接continue！！！
     * -- 注意，dfs处理完当前string后，一定要将其从空串复原， 且 复原 tempResult ！！！
     * --- 定义全局 Boolean变量，剪枝 的同时，保证全局变量result 只会赋值一次 !!
     *
     * @param tickets
     * @return
     */
    static List<String> result;

    static boolean hasFind;

    public static List<String> findItinerary2(List<List<String>> tickets) {
        result = new ArrayList<>();
        hasFind = false;
        HashMap<String, ArrayList<String>> map = new HashMap<>();
        for (List<String> ticket : tickets) {
            String from = ticket.get(0);
            String to = ticket.get(1);
            if (map.containsKey(from)) {
                map.get(from).add(to);
            } else {
                ArrayList<String> list = new ArrayList<>();
                list.add(to);
                map.put(from, list);
            }
        }
        //对list进行排序
        for (Map.Entry<String, ArrayList<String>> entry : map.entrySet()) {
            ArrayList<String> value = entry.getValue();
            value.sort((x, y) -> x.compareTo(y));
        }
        String from = "JFK";
        int targetLength = tickets.size() + 1;
        ArrayList<String> tempResult = new ArrayList<>();
        tempResult.add(from);
        dfs2(tempResult, map, from, targetLength);
        return result;
    }

    private static void dfs2(ArrayList<String> tempResult, HashMap<String, ArrayList<String>> map, String
            from, int targetLength) {
        // 定义全局变量，剪枝 的同时，保证全局变量result 只会赋值一次
        if (hasFind) {
            return;
        }
        if (tempResult.size() == targetLength) {
            result = new ArrayList<>(tempResult);
            hasFind = true;
            return;
        }
        if (!map.containsKey(from)) {
            return;
        }
        ArrayList<String> list = map.get(from);
        for (int i = 0; i < list.size(); i++) {
            String to = list.get(i);
            if (to.length() == 0) {
                continue;
            }
            // 修改为空串，表明 改 string 已经访问过了
            list.set(i, "");
            tempResult.add(to);
            dfs2(tempResult, map, to, targetLength);
            // 复原 tempResult
            tempResult.remove(tempResult.size() - 1);
            //注意，dfs处理完当前string后，一定要将其从空串复原！！！
            list.set(i, to);
        }
    }

    /******************* 代码优化  *****************************************/
    /**
     * Hierholzer 算法用于在连通图中寻找欧拉路径，其流程如下：
     * 1、从起点出发，进行深度优先搜索。
     * 2、每次沿着某条边从某个顶点移动到另外一个顶点的时候，都需要删除这条边。
     * 3、如果没有可移动的路径，则将所在节点加入到栈中，并返回。
     * <p>
     * 当我们顺序地考虑该问题时，我们也许很难解决该问题，因为我们无法判断当前节点的哪一个分支是「死胡同」分支。
     * 不妨倒过来思考。我们注意到只有那个入度与出度差为 11 的节点会导致死胡同。而该节点必然是最后一个遍历到的节点。
     * 我们可以改变入栈的规则，当我们遍历完一个节点所连的所有节点后，我们才将该节点入栈（即逆序入栈）。
     * 对于当前节点而言，从它的每一个非「死胡同」分支出发进行深度优先搜索，都将会搜回到当前节点。
     * 而从它的「死胡同」分支出发进行深度优先搜索将不会搜回到当前节点。
     * 也就是说当前节点的死胡同分支将会优先于其他非「死胡同」分支入栈。
     * 这样就能保证我们可以「一笔画」地走完所有边，最终的栈中逆序地保存了「一笔画」的结果。我们只要将栈中的内容反转，即可得到答案。
     */
    static List<String> list;
    //定义一个优先级队列，用来存放to集合
    static HashMap<String, PriorityQueue<String>> map;

    public static List<String> findItinerary(List<List<String>> tickets) {
        list = new ArrayList<>();
        map = new HashMap<>();
        for (List<String> ticket : tickets) {
            String from = ticket.get(0);
            String to = ticket.get(1);
            if (!map.containsKey(from)) {
                map.put(from, new PriorityQueue<>());
            }
            map.get(from).offer(to);
        }
        dfs("JFK");
        return list;
    }

    private static void dfs(String from) {
        while (map.containsKey(from) && !map.get(from).isEmpty()) {
            dfs(map.get(from).poll());
        }
        //头插法
        list.add(0, from);
    }
}
```

