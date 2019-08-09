# [Leetcode 199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)

Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

**Example:**
```
Input: [1,2,3,null,5,null,4]
Output: [1, 3, 4]
Explanation:

   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
```

## 思路

我们发现`right side view`由每一层最右边的元素组成。鉴于此，我们可以对树进行level-order traversal，在访问每一层节点时，
取出最右边的元素放入返回答案列表。

为了按层遍历整个树，我们需要对基础的BFS做出修改。BFS会自动按层遍历整个数组，但是我们无法知道当前在哪一层。
因此，一个简单且巧妙的方法为记录每层节点数量，每次访问完这个数量的节点后，我们即可知道当前这层已经访问完成。

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
    public List<Integer> rightSideView(TreeNode root) {
        
        List<Integer> res = new LinkedList<Integer>();
        if (root == null) return res; 
        
        LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
        queue.add(root);
        
        while (! queue.isEmpty()) { 
            
            // get right most element in the current level
            int levelCount = queue.size();
            TreeNode rightmost = queue.getLast();
            res.add(rightmost.val);
             
            // only pop current level's elements from the queue
            for (int i = 0; i < levelCount; i++) {
                TreeNode node = queue.removeFirst();
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
            }
        }
        
        return res;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). 我们需要按层遍历所有n个节点。

- **Space Complexity:** O(n). Queue中最多储存所有的O(n)个节点。

## 拓展

我们可以用类似的思想，实现`left-side view`吗？或者`bottom-view`？需要修改那一行（几行）代码？

## 总结

大量树这个类别的题目都在考察BFS，DFS算法的数量应用。我们要掌握它们的多种变体和常用的修改手段，比如这里的按层遍历。