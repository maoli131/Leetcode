# [Leetcode 250. Count Univalue Subtrees](https://leetcode.com/problems/count-univalue-subtrees/)

## 题目

Given a binary tree, count the number of uni-value subtrees.

A Uni-value subtree means all nodes of the subtree have the same value.

**Example:**
```
Input:  root = [5,1,5,5,5,null,5]

              5
             / \
            1   5
           / \   \
          5   5   5

Output: 4
```

## 思路

这道题和[208-MostFrequentSubTreeSum](https://leetcode.com/problems/most-frequent-subtree-sum)非常相似。我们可以利用递归的方式
DFS遍历子树来判断左右子树是否为uni-value。如果左右两边都是uni-value，我们则需检查当前节点的值与左右子节点是否相同，如果相同，当前节点为根的树
同样也是uni-value。我们可以得到如下的解答。

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
    private int count = 0;
    
    private boolean isUnivalue(TreeNode node) {
        if (node.left == null && node.right == null) {
            count++;
            return true;
        }
        boolean uni = true;
        if (node.left != null) {
            uni = isUnivalue(node.left) && uni && node.val == node.left.val;
        }
        if (node.right != null) {
            uni = isUnivalue(node.right) && uni && node.val == node.right.val;
        }
        
        count = (uni) ? count + 1 : count;
        return uni;
    }
    
    public int countUnivalSubtrees(TreeNode root) {
        if (root == null) return 0;
        isUnivalue(root);
        return count;
    }
    
}
```
## Complexity Analysis

- **Time Complexity:** O(n). 访问单个节点时间为O(1)，总共需要访问所有的节点，即O(n).

- **Space Complexity:** O(log(n)). 树的每一层我们都需要递归，因此递归需使用树高O(log(n))这么大的stack space.

## 拓展

如果你觉得复杂的一连串的条件检查非常不好，我们可以进一步优化以上的算法。我们可以在recursive call的时候pass-in parent的`val`，
用它来和当前节点进行对比，以返回帮助parent判断其是否为uni-value. 代码如下：

```java
class Solution {
    private int count = 0;
    
    private boolean uniSub(TreeNode node, int parentVal) {
        if (node == null) return false;
        // VERY IMPORTANT: use | instead of ||. | doesn't short circuiting. 
        if (! uniSub(node.left, node.val) | ! uniSub(node.right, node.val)) {
            return false;
        }
        count++;
        return node.val == parentVal;
    }
    
    public int countUnivalSubtrees(TreeNode root) {
        uniSub(root, 0);
        return count;
    }
}
```

## 总结

这道题同样为考察DFS树遍历。不过我们需要根据题目设置合理的条件判断，不多不少刚刚好，并尝试进行优化以得出优美的解答。

**Note:** VERY IMPORTANT!!! 这道题还需要注意的是条件判断时，&& 和 || 会short-circuits但是 & 和 | 不会。
递归的时候一定注意不要因为short-circuiting而没执行某些部分。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/count-univalue-subtrees/solution/)