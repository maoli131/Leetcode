# [Leetcode 226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree)

## 题目

Invert a binary tree.

**Example:**
Input:
```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```
Output:
```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

**Trivia:**
This problem was inspired by a post from Max Howell: 
```
Google: 90% of our engineers use the software you wrote (Homebrew), 
but you can’t invert a binary tree on a whiteboard so f*** off.
```

## 思路

对于root来讲，要翻转整个树，我们只需要单独把左边、右边的subtree都各自翻转，然后将他们交换。这
启发我们再一次使用万能的递归。当然，既然可以使用递归，我们也可以使用循环。我们可以使用BFS的想法，
从root开始依次对每层的每个node将其children左右互换。

## 解答
Recursive Solution:
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
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;
        TreeNode temp = root.right;
        root.right = invertTree(root.left);
        root.left = invertTree(temp);
        return root;
    }
}
```
Iterative Solution:
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.add(root);
        while (! queue.isEmpty()) {
            TreeNode curr = queue.poll();
            TreeNode tmp = curr.right;
            curr.right = curr.left;
            curr.left = tmp;
            if (curr.left != null) queue.add(curr.left);
            if (curr.right != null) queue.add(curr.right);
        }
        return root;
    }
}
```

## Complexity Analysis:
- **Time Complexity:** O(n). 两种算法的时间复杂度都是O(n)，因为我们必须遍历所有的节点来将他们翻转。
- **Space Complexity:** 
  * 递归解法：O(log(n)). 我们至少需要树高大小的stack space
  * 循环解法：O(n). 在最坏情况下，我们的queue中会存储一整层的树节点 ~ n/2

## 拓展
- 使用DFS应该如何解决这道题？
- 我们可以向右旋转这个Binary Tree吗？

## 总结

一如往常，当我们把这个问题从全局缩小到局部，从大问题化简为好解决的小问题之后，解法跃然纸上：递归或者BFS。
我们可以把这类简单的基础题型作为Building Block来解决其他更复杂的问题。

这道题如果出现在Google的白班面试中，我们其实可以直接把白板翻转过来。。(cr. a twitter post)