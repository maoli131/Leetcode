# [Leetcode 285. Inorder Successor in BST](https://leetcode.com/problems/inorder-successor-in-bst)

## 题目

Given a binary search tree and a node in it, find the in-order successor of that node in
that BST. The successor of a node p is the node with the smallest key greater than p.val

**Example 1:**
```
Input: root = [2,1,3], p = 1
Output: 2
Explanation: 1's in-order successor node is 2. Note that both p and the return value is of TreeNode type.
```
**Example 2:**
```
Input: root = [5,3,6,2,4,null,null,1], p = 6
Output: null
Explanation: There is no in-order successor of the current node, so the answer is null.
```
**Note:**
1. If the given node has no in-order successor in the tree, return `null`.
2. It's guaranteed that the valuess of the tree are unique.

## 思路

这道题初见觉得非常简单：找到整个树中比`p`大的所有节点中最小节点即可。很直观的想法是直接分情况讨论，
比如看`p`有没有左子树，如果有则在左子树中找最小的元素；如果没有左子树，则需要依据`p`的`parent`
节点来继续分情况讨论。但是我们会发现，这样情况会变得越来越复杂，而且很容易出错。

有没有更直接的方法来解决这道题呢？我们需要重新反思Binary Tree Search去找节点`p`的这个过程。
我们发现，当我们往当前节点`curr`的左子树寻找`p`时，`curr`其实是比`p`大的节点中的一员。这使得
`curr`成为`p`当前最有可能的`successor`，因此我们应该将`curr`记录下来。当我们前往右子树寻找`p`时，
`curr`则一定不是`successor`。这样，我们在寻找`p`的过程中不断更新其`successor`的最佳人选，
最终当我们找到`p`的时候我们已经找到了其`successor`。

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
    
    // Iterative Solution
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        TreeNode succ = null;
        while (root != null) {
            if (p.val < root.val) {
                succ = root;
                root = root.left;
            }
            else root = root.right;
        }
        return succ;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(log(n)). 我们用二分搜索的方式找到了节点`p`，并在此过程中找到了其`successor`。
- **Space Complexity:** O(1). 我们在此过程中并没有使用额外的空间。

## 拓展

我们可以使用递归的方式解答这道题吗？我们可以将这个问题分解为`p`在`root`的左边和右边两种情况。我们
知道如果`p`在右边，那么我们可以直接可以在右子树上递归寻找继承者；而如果`p`在左边且`p`是左边子树的
最大值，那么`root`应该是`p`的继承者。其他情况下，我们可以继续递归寻找左边继承者。代码如下：
```java
class Solution {
    
    // Recursive Solution
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        if (root == null) return null;
        if (p.val >= root.val) return inorderSuccessor(root.right, p);
        else {
            TreeNode leftSucc = inorderSuccessor(root.left, p);
            return (leftSucc == null) ? root : leftSucc;
        }
    }
}
```

## 总结

分情况讨论在有些题目上非常有效，但是在这类题型上反而会因为情况太多而导致解答出错。我们应该首先抓住
问题的本质，比如这题结合Tree Search的特性来找继承者，在思考用循环或递归的方式解答。

## Reference

- Leetcode [Discussion](https://leetcode.com/problems/inorder-successor-in-bst/discuss/?currentPage=1&orderBy=most_votes&query=)