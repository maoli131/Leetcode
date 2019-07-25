# [Leetcode 303. Range Sum Query](https://leetcode.com/problems/range-sum-query-immutable/)

## 题目

Given an integer array *nums*, find the sum of the elements between indices *i* 
and *j* *(i<j)*, inclusive.

**Example:**
```
Given nums = [-2, 0, 3, -5, 2, -1]

sumRange(0, 2) -> 1
sumRange(2, 5) -> -1
sumRange(0, 5) -> -3
```
**Note:**
1. You may assume that the array does not change.
2. There are many calls to *sumRange* function.

## 思路

这道题目也是初见感觉非常简单，我们可以直接用循环的方式去把范围内元素全部相加。但是，我们忽视了题目
中重要的一个要求: `sumRange` will be called many times. 我们的naive方法会不断重复计算我们
已知的和，花费大量不必要的时间。

仔细观察我们会发现，在`nums`不变的情况下，我们可以将一些中间计算结果保存下来，以提高每次`sumRange`
的效率。中间的计算结果应该包括什么呢？我们发现`sumRange`需要求[i, j]范围内的和，而这范围内的和等于
[0, j]之和减去[0, i - 1]之和。即：`sum(i, j) = sum(0, j) - sum(0, i - 1)`. 因此，我们
可以另建一个`private array`，每个位置`k`保存`sum(0, k)`。

## 解答
```java
class NumArray {

    private int[] sum;

    public NumArray(int[] nums) {
        sum = new int[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            sum[i + 1] = sum[i] + nums[i];
        }
    }

    public int sumRange(int i, int j) {
        return sum[j + 1] - sum[i];
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```
这里有一个小细节值得注意：我们之前的解答思路里面需要使用`sum(j, 0) - sum(i - 1)`，但是`i - 1`
在`i = 0`的情况下会超出数组边界，这要求我们每次使用`i`的时候都需要用if条件去检查。于是，我们将`sum`数组
的最左边加了一个额外的0号位，将数组整体向右移动了一位，以避免反复的条件检查。

## Complexity Analysis
- **Time Complexity:** O(n). 在创建NumArray的时候，我们循环了整个数组来将中间和存储在sum中; 不过这样使得我们每次`sumRange`仅仅需要O(1)的时间。
- **Space Complexity:** O(n). 我们使用了额外的数组sum来存储n个中间和。

## 拓展
- 在以上的解答中我们使用了额外的空间。我们有其他办法减少空间的使用吗？
- 时间复杂度为O(n)的解答是最优解吗？

## 总结

对于这类题型，我们需要深入理解题目要求。当我们发现最终的计算结果包括了大量重复运算的时候，我们可以
思考将中间结果保存起来，让未来的计算更加的高效。这是动态规划的核心思想。