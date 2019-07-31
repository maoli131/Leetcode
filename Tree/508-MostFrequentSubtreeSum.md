# [Leetcode 508. Most Frequent Subtree Sum](https://leetcode.com/problems/most-frequent-subtree-sum)

## 题目

Given the root a tree, you are asked to find the most frequent subtree sum. The 
subtree sum of a node is defiend as the sum of all the node values formed by the
subtree rooted at that node (including the node itself). So what is the most frequent
subtree sum value? If there is a tie, return all the values with the highest frequency
in any order.

**Example 1:**
Input:
```
  5
 /  \
2   -3
```
return [2, -3, 4], since all the values happen only once, return all of them in any order.

**Example 2:**
Input:
```
  5
 /  \
2   -5
```
return [2], since 2 happens twice, however -5 only occur once.
**Note:** You may assume the sum of values in any subtree is in the range of 32-bit signed integer.

## 思路

我们可以将题目拆分为求TreeSum和找频率最高的TreeSum两个部分。前者比较直观，给定一个根节点，我们
可以对树进行递归的post-order traversal，在过程中求和，并记录当前最高出现频率`maxCount`。
后者需要我们记录每个和出现的次数，即为`(sum, count)` pair. 为了更快的查找和、更新其出现次数，
我们可以使用HashMap来将`sum`作为key，`count`作为value. 

将两部分结合在一起，我们可以得到具有全部子树和及其对应出现次数的HashMap，然后将出现`maxCount`次的
子树和提取出来，放进数组并返回结果。

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
    
    int maxCount = 0;
    HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
    LinkedList<Integer> arr = new LinkedList<Integer>();
    
    public int[] findFrequentTreeSum(TreeNode root) {  
        treeSum(root);
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            // get all sums that has maxCount
            if (entry.getValue() == maxCount) {
                arr.add(entry.getKey());
            }
        }

        // return the result as an int array
        int[] res = new int[arr.size()];
        for (int i = 0; i < arr.size(); i++) {
            res[i] = arr.get(i);
        }
        return res;
    }
    
    // find the sum of given subtree
    private int treeSum(TreeNode node) {
        if (node == null) return 0;
        int s = treeSum(node.left) + treeSum(node.right) + node.val;
        int freq = map.getOrDefault(s, 0) + 1; // update (sum, count) pair
        map.put(s, freq);
        maxCount = Math.max(maxCount, freq); // update current max
        return s;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). 树求和的过程需要递归遍历所有节点、从HashMap中提取MaxCount同样需要遍历所有子树和。两个为并列的O(n), 即总时间为O(n).

- **Space Complexity:** O(n). 我们使用递归进行树求和，树有O(logn)层，则递归需要占用O(log(n))的stack space. 同时我们使用HashMap储存所有子树和，最坏情况下可能有n个子树和，因此占用空间O(n).

## 拓展

如果题目要求求第n高频率的子树和呢？可以目前的解法中做哪些修改？

## 总结

这道题目基本上又是DFS、BFS的变体题型。我们需要熟练运用这两种树的遍历算法，再基于此针对题目要求做一些改进，比如结合HashMap记录子树和出现次数。

## Reference

- [lee215's solution](https://leetcode.com/problems/most-frequent-subtree-sum/discuss/98675/JavaC%2B%2BPython-DFS-Find-Subtree-Sum)