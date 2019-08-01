# [Leetcode 50. Power(x, n)](https://leetcode.com/problems/powx-n/)

## 题目

Calculate `pow(x, n)` which is x raised to the power of n.

**Example 1:**
```
Input: 2.00000, 10
Output: 1024.00000
```
**Example 2:**
```
Input: 2.10000, 3
Output: 9.26100
```
**Example 3:**
```
Input: 2.00000, -2
Output: 0.25000
Explanation: 2^(-2) = (1/2)^2 = 1/4 = 0.25
```

**Note:**
- -100.0 < x < 100.0
- n is a 32-bit signed integer, within the range [(-2)^31, 2^31 - 1]

## 思路

这道题最直接的思路为用循环来乘n次x来得到结果。即使在n为负的情况下，我们仅需将n替换为-n，将x替换为1/x.
这种方法时间复杂度为O(n)，显然在题目给定的range上，这个方法太慢了。

我们可以结合简单的数学来对以上算法进行优化。我们注意到：
```
if n is even: 
    x^n = x^(n / 2) * x^(n / 2)
if n is odd:
    x^n = x^( (n-1) / 2) * x^( (n-1) / 2)  * x
```
这提示我们可以将pow(x, n)化分为性质相同、大小减半的子问题来解决，即Divide and Conquer（分治）的思想。
我们可以用递归来实现这个算法，如下

## 解答
```java
class Solution {
    public double myPow(double x, int n) { 
        long N = n;     // prevent integer overflow
        if (N < 0) {
            N = -N;
            x = 1.0 / x;
        }
        return fastPow(x, N);
    }
    private double fastPow(double x, long n) {
        if (n == 0) return 1;
        double half = fastPow(x, n / 2);
        if (n % 2 == 0) 
            return half * half;
        else
            return half * half * x;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(log(n)). 我们每次递归都将问题大小缩小为了原来的一半，因此总共需要O(log(n))时间。

- **Space Complexity:** O(log(n)). 我们需要O(log(n))的stack space因为做了这么多次的递归操作。

## 拓展

我们主要到递归占用了非常多的stack space。为了节省空间，我们可以用循环来实现这个算法吗？

## 总结

这种看似简单的题目其实深坑非常多。首先，我们需要考虑我们解法的性能，比如时间空间复杂度。其次，我们还需要考虑
各种极端的边界情况，比如Integer overflow等等问题。这道题目中我们就使用了`long`来避免这个错误。
这需要大量的积累与总结。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/powx-n/solution/)
