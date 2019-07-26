# [Leetcode 261. Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)

## 题目

Given `n` nodes labeled from `0` to `n - 1` and a list of undirected edges (each edge
is a pair of nodes), write a function to check whether these edges make up a valid tree.

**Example 1:**
```
Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]
Output: true
```
**Example 2:**
```
Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]
Output: false
```
**Note:** You can assume that no duplicate edges will appear in `edges`. Since all edges
are undirected, `[0,1]` is the same as `[1,0]` and thus will not appear together in `edges`.

## 思路

这是一道经典的图表题。要判断一个Graph是不是Tree，我们需要知道树的两个充分必要条件:
1. 有且仅有一个connected component.
2. 图形中没有环.

要满足这两个条件，首先，这个图形`edges`的数量必须恰好等于`node`的数量减一。在这道题目中，即：
`edges.length = n - 1`。当`edges`数量小于`n - 1`时，必然有节点没有与其他节点相连，违反了
树的第一条性质；当`edge`数量大于`n - 1`的时候，我们可以用鸽子洞理论证明，图形中必然有环，违反了
树的第二条性质。

其次，`edges`数量恰好等于`n-1`并不表示图像中一定没有环。我们需要用其他的方法来检查图形中是否有
环。我们可以采用`union-find`的思想。我们创建一个新的数组`unions`，对于每条`edge (u, v)`，
我们令`unions[v] = u`。这样我们记录了`u`的`parent`为`v`。这样，在遇见新的`edge (x, y)`时，
我们利用`unions`来追溯`x`和`y`最深的祖先，如果他们有同一个祖先，则`edge (x, y)`必定造成环。

当`edges.length == n - 1`并且检查所有`edges`都不构成环的时候，我们就可以说这个图形一定为树。

## 解答
```java
class Solution {
    
    public boolean validTree(int n, int[][] edges) {
        
        if (edges.length != n - 1) return false;

        // initialize all unions
        int[] unions = new int[n];
        Arrays.fill(unions, -1);
        
        // check all edges for cycle and construct the unions
        for (int[] edge : edges) {
            int uGroup = find(unions, edge[0]);
            int vGroup = find(unions, edge[1]);
            if (uGroup == vGroup) return false;
            else unions[vGroup] = uGroup;
        }
        return true;
    }
    
    // find the deepest parent which vetex belongs to (the union group)
    private int find(int[] unions, int vertex) {
        while(unions[vertex] != -1) {
            vertex = unions[vertex];
        }
        return vertex;
    }
}
```
在这个解法中，我们注意到在`unions`数组中，我们并未对每个`unions[v]`保存其直接的`parent u`，
而是保存了`v`通过`find`找到的最深的祖先。直观上理解，我们可以认为`u`与`v`原本存在于不同的集合，
各自集合的代表为其最深的祖先。当我们遇见`edge (u, v)`，我们则将两个集合合并在了一起，让`u`的祖先
为这个新集合的代表。

## Complexity Analysis
- **Time Complexity:** O(E * V). 我们循环遍历了所有的边，并且对于每条边，我们需要通过`find`寻找
  边两个端点的最深祖先(最坏情况下会找V次)，因此时间复杂度为O(EV).
- **Space Complexity:** O(V). 我们创建了新的数组来储存所有节点的祖先。

## 拓展

- 我们可以使用BFS和DFS解决这道题吗？这两种解法的时间空间复杂度又是多少呢？
- 更多关于`union-find`以及`disjoint set`的图解及示例请参见[这里](https://www.geeksforgeeks.org/union-find/)

## 总结

解决这类问题我们需要非常清楚各类图形的概念，深入掌握他们的特征。
同时，我们还需要对`union-find`这类方法非常熟悉，能够手到擒来。