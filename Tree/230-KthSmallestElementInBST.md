# [Leetcode 230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst)

## 题目

Given a binary search tree, write a function kthSmallest to find the **k**th smallest element in it.

**Note:**
You may assume k is always valid, 1 <= k <= BST's total elements. 

**Example 1:**
```
Input: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
Output: 1
```
**Example 2:**
```
Input: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
Output: 3
```

## 思路

这道题最直观的一种解法为顺序遍历整个二叉树，然后直接返回第n小的元素。我们可以用递归来实现这个顺序
遍历。不过，这样时间、空间复杂度均为O(n)，因为我们需要遍历整个二叉树，并且还得将所得的正序数组
保存起来。

如果我们只想要第k小的元素，我们其实只需要将DFS和顺序遍历结合在一起，找到第k小的元素后直接终止我们
的遍历即可。如果我们从root开始向最左边的path进行DFS，那么我们从DFS的stack中pop出的第k个元素
即为第k小的元素。这样我们能极大降低时间复杂度。

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
    public int kthSmallest(TreeNode root, int k) {
        TreeNode node = root;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        while (true) {
            while (node != null) {
                stack.push(node);
                node = node.left;
            }
            node = stack.pop();
            k--;
            if (k == 0) return node.val;
            node = node.right;
        }
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(k + log(n)). 首先我们需要DFS找到树中最小的元素，这需要O(log(n))的时间。然后我们要从stack中pop出k个元素这又需要O(k)的时间。
- **Space Complexity:** O(n). 我们在DFS过程中使用的stack在最坏情况下要存储所有的元素。

## 拓展

What if the BST is smodified (insert/delete operations) often and you need to find the kth smallest frequently? 
How would you optimize the kthSmallest routine?

## 总结

我们在做这类第k小、第k大的题目时，需要灵活运用inorder, preorder, postorder三种树遍历的方法，并对特定问题进行优化。比如这道题目我们利用了
Stack的特性，只取k个元素出来，极大了减少运行时间。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/kth-smallest-element-in-a-bst/solution/)