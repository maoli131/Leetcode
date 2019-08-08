# [Leetcode 207. Course Schedule](https://leetcode.com/problems/course-schedule)

## 题目

There are a total of *n* course you have to take, labeled from `0` to `n-1`.

Some courses may have prerequisites, for example to take course 0 you have to first
take course 1, which is expressed as a pair: `[0, 1]`.

Given the total nunber of courses and a list of prerequisite **pairs**, is it possible 
for you to finish all courses?

**Example 1:**
```
Input: 2, [[1,0]] 
Output: true
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0. So it is possible.
```
**Example 2:**
```
Input: 2, [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0, and to take course 0 you should
             also have finished course 1. So it is impossible.
```
**Note:**
1. The input prerequisites is a graph represented by **a list of edges**, not adjacency matrices. Read more about [how a graph is represented](https://www.khanacademy.org/computing/computer-science/algorithms/graph-representation/a/representing-graphs)
2. You may assume that there are no duplicate edges in the input prerequisites.

## 思路

这道题本质上考察的是**Topological Sort**. Prerequisites表示了一个Dircted Graph，每个节点为一节课，连线为(Course -> Prereq)。我们想
在这个图上找到某种上课顺序，使得前后课程不会互为Prerequisite；即我们要对图像进行拓扑排序，并判断这个图像中有没有循环。

我们可以采用BFS来解决这个问题。首先，我们要记录所有节点的`in-degree`。当一个节点的`in-degree`为0时，我们知道它不被任何其他课程所依赖。因此，
我们可以把它和它的所有`edges`从图中移除（更新与它相邻的`node`的`in-degree`）。然后，我们不断重复这个过程，持续将新出现的`in-degree`为0的节点
放入BFS的queue中，进行移除操作并更新它邻居的`in-degree`。如果我们发现如果还有节点未被访问，且不存在`in-degree`为0的节点，我们可以断定图像中一定有环。
如果我们能够在没有环的情况下访问完所有的节点，说明我们找到了图像的拓扑排序。

## 解答
```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        
        // Construct graph and find starting root
        int[][] adjMatrix = new int[numCourses][numCourses];
        int[] indegree = new int[numCourses];
        for (int[] pair : prerequisites) {
            adjMatrix[pair[0]][pair[1]] = 1; // course -> prereq
            indegree[pair[1]]++; // one more course depends on it
        }
        
        // BFS: start with all roots (indegree = 0)
        Queue<Integer> queue = new LinkedList();
        for (int i = 0; i < indegree.length; i++) {
            if (indegree[i] == 0) queue.offer(i);
        }
        
        // Standard BFS:
        int count = 0;
        while (! queue.isEmpty()) {
            int course = queue.poll();
            count++;
            for (int i = 0; i < numCourses; i++) {
                // node i now has no incoming edges, add to queue
                if (adjMatrix[course][i] == 1 && --indegree[i] == 0) {
                    queue.offer(i);
                }
            }
        }
        
        return count == numCourses; // visited all courses without circle
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). 建立Graph和Indegree数组都需要遍历所有元素，同时BFS也会访问每个节点，总共时间为O(n).

- **Space Complexity:** O(n). 我们使用了额外的二位数组、队列来储存所有的节点和边。

## 拓展

我们以上用BFS实现了拓扑排序。你能用DFS实现它吗？我们从没被访问过的节点开始，连续访问它的所有相邻节点。如果访问到之前访问过的节点，则有循环；
如果没有，我们从另一个未被访问过的节点开始继续深度优先搜索。

## Reference

- [jianchao-li's solution](https://leetcode.com/problems/course-schedule/discuss/58509/C%2B%2B-BFSDFS)
- [justjiayu's solution](https://leetcode.com/problems/course-schedule/discuss/58516/Easy-BFS-Topological-sort-Java)