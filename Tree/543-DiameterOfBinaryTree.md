# [Leetcode 543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree)

## 题目

Given a binary tree, you need to compute the length of the tree's diameter. The diameter of a binary
tree is the length of the **longest** path between any two nodes in a tree. This path may or
may not path through the root.s

**Example:**
```
          1
         / \
        2   3
       / \     
      4   5  
```
return **3**, which is the length of the path [4,2,13] or [5,2,1,3]

**Note:**
The length of the path between two node is represented by the number of edges between them.

## 思路

对于这类题型，个人倾向于将它拆分为三个部分来看待。对于root来讲，最长的路径可能存在于：
- 左边的子树, left sub tree
- 右边的子树，right sub tree
- 或者经过root，贯穿左右

这样的模式很容易启发我们使用递归。前两个情况可以被递归解决，但是第三种情况如何处理呢？已知最长路径
贯穿root，那么我们知道左边的路径一定是从root到最远的leaf，右边同理，否则这路径一定不是最长的。
因此，我们发现第三种情况为左右子树高度相加再加1。

值得注意的是，因为我们想求最长路径，我们需要将三种情况进行比较，并在递归的过程中，保存一个全局的
变量来记录最长。

## 解答
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    
    private static int longestPath = 0;
    
    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return longestPath;
    }

    private int maxDepth(TreeNode node) {
        if (node == null) return 0;
        int left = maxDepth(node.left);
        int right = maxDepth(node.right);
        
        // Compare case 3 with global max
        longestPath = Math.max(longestPath, left + right);

        return Math.max(left, right) + 1;
    }
}
```

## Complexity Analysis
- **Time Complexity:** O(n). 我们遍历了树上的所有节点。
- **Space Complexity:** O(n). 我们递归的stack space需要储存所有的节点。

## 拓展

- 运用循环的方式能解决这道题吗？循环能够减少空间的使用吗？
- 用BFS能解决这道题吗？
- O(n)的时间复杂度是最优解吗？

## 总结

这道题是经典的DFS的应用题。一如既往，我们依然尝试将问题拆分成几种不同的情况，并观察其中大部分情况是否
是递归的解答。然后，对于剩下的某种、某几种无法递归得出答案的解答，我们则去思考如何利用现有条件求解。
最终，我们把所有的情况整合在一起，得出整个题目的答案。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/diameter-of-binary-tree/solution)