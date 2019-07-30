# [Leetcode 103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

## 题目

Given a binary tree, return the *zigzag level order* traversal of its nodes' valuess. 
(ie. from left to right, then right to left for the next level and alternate between).

For example:

Given binary tree `[3, 9, 20, null, null, 15, 7]`
```
    3
   / \
  9  20
    /  \
   15   7
```
return its zigzag level order traversal as:
```
[
  [3],
  [20,9],
  [15,7]
]
```

## 思路

题目条件的*level order*提示我们需要用广度优先算法**BFS**。不过，虽然BFS能帮助我们按层遍历二叉树，
我们仍然需要结合其他的方式来实现*zig-zag*。我们注意到，在奇数层遍历顺序为从左到右，偶数层顺序为从右
到左。因此，在使用BFS的时候，我们需记录当前层级的信息。每走完一层的节点，我们则改变遍历顺序。
在BFS中记录层级有非常多不同的方法。比如可以往BFS的Queue中，每两层节点之间放置空节点；比如可以提前记录
每层节点个数，然后在每层仅遍历相应数量的节点。我们下面先采用第二种方法。

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
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) return ans;

        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.add(root);
        int size = queue.size();
        boolean leftToRight = true;
        
        while (! queue.isEmpty()) {     
            // iterate all nodes in current lvel
            List<Integer> level = new LinkedList<Integer>();
            for (int i = 0; i < size; i++) {          
                TreeNode curr = queue.poll();
                if (leftToRight) level.add(curr.val); // zig
                else level.add(0, curr.val); // zag
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }    
            size = queue.size(); // now queue only contains nodes in next level
            leftToRight = ! leftToRight; // flip order
            ans.add(level);
        }

        return ans;
    }
}
```

## Complexity Analysis
- **Time Complexity:** O(n). 每个节点的enqueue, dequeue都为O(1)，而我们需要访问全部节点。
- **Space Complexity:** O(n). 在最坏的情况下，我们使用的queue需要存最后一层的所有节点，占用空间为O(n)。

## 拓展

在以上的解法中，我们用**循环**的方式实现了BFS，利用每层节点数量来巧妙记录了层级信息，并以此实现遍历
顺序的翻转(zig-zag)。你可以使用**递归**的方式实现BFS并同时记录层数吗？

## 总结

对于树形题目，我们需首先大致判断是否需要使用BFS或者DFS算法。我们尤其需要注意比如`level-order`，
`nth-smallest`这样的提示词。在得出大方向之后，我们再根据题目要求对BFS、DFS做出一定的修改，比如
记录每层节点、翻转遍历方向等等。

## Reference

- [GraceLuLi's solution](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/discuss/33814/A-concise-and-easy-understanding-Java-solution)
- [awaylu's solution](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/discuss/33815/My-accepted-JAVA-solution)