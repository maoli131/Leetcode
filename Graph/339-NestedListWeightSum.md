# [Leetcode 339. Nested List Weight Sum](https://leetcode.com/problems/nested-list-weight-sum/)

## 题目

Given a nested list of integers, return the sum of all integerss in the list weighted
by their depth. 

Each element is either an integer, or a list -- whose elements may also be integers or 
other lists.

**Example 1:**
```
Input: [[1,1],2,[1,1]]
Output: 10 
Explanation: Four 1's at depth 2, one 2 at depth 1.
```
**Example 2:**
```
Input: [1,[4,[6]]]
Output: 27 
Explanation: One 1 at depth 1, one 4 at depth 2, and one 6 at depth 3; 1 + 4*2 + 6*3 = 27.
```

## 思路

题目要求我们对nestedList进行求和。仔细读题我们发现，nestedList中要么为普通的Integer（我们可以直接求和），
要么为下一层nestedList（需要我们继续用相同的方法求和）。这提示我们应用递归的思想，因为我们在不断的处理性质相同的问题。
不过，值得注意的是，求和时需要将每个元素按照其深度加权。递归恰好能帮我们完成这一点：我们只需要在递归/DFS时，记录当前层数即可。

## 解答
```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *     // Constructor initializes an empty nested list.
 *     public NestedInteger();
 *
 *     // Constructor initializes a single integer.
 *     public NestedInteger(int value);
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // Set this NestedInteger to hold a single integer.
 *     public void setInteger(int value);
 *
 *     // Set this NestedInteger to hold a nested list and adds a nested integer to it.
 *     public void add(NestedInteger ni);
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
class Solution {
    
    public int depthSum(List<NestedInteger> nestedList) {
        return dfs(nestedList, 1);
    }
    
    // Recursively unnest list and find sum
    private int dfs(List<NestedInteger> nestedList, int level) {
        if (nestedList.size() == 0) return 0;
        int sum = 0;
        for (NestedInteger ni : nestedList) {
            if (ni.isInteger()) {                
                sum += (ni.getInteger() * level);
            }
            else {
                sum += dfs(ni.getList(), level + 1);
            }
        }
        return sum;
    }
}
```

## Complexity Analysis:

- **Time Complexity:** O(n). 我们需要通过DFS遍历所有的NestedElement（包括Integer和NestedList）。

- **Space Complexity:** O(n). 递归每往下一层就需要占用一次stack space，因此最坏情况下需要O(n)的栈空间。

## 拓展

- 如果不用递归，而仅采用循坏，我们可以解决这道题吗？两者有什么区别呢？

## 总结

这类题目相对基础，但是依然需要我们核心的两个步骤：一为判断出这道题目考察知识点为Graph, DFS和递归；
二为我们熟练地bug-free地完成代码的书写。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/nested-list-weight-sum/solution/)