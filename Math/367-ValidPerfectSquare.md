# [Leetcode 367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square)

## 题目

Given a positive integer *num*, write a function which returns True if *num* is 
a perfect square else false.

**Note: DO not** use any built-in library function such as sqrt.

**Example 1:**
```
Input: 16
Output: true
```
**Example 2:**
```
Input: 14
Output: False
```

## 思路

这道题目最直观的解法为在[0, num]诶个尝试所有树以找到`num`的根，但是这个O(n)的解法显然太过缓慢。
为了优化这个思路，我们可以应用二分搜索法，将时间复杂度降到O(log(n))。下面的解答应用的就是这种方法。

不过，我们应用数学上更巧妙的方法解答这道题，详见[拓展](##拓展)。

## 解答
```java
class Solution {
    public boolean isPerfectSquare(int num) {
        int low = 1, high = num;
        while (low <= high) {
            long mid = (low + high) / 2;
            if (mid * mid == num) {
                return true;
            } else if (mid * mid < num) {
                low = (int) mid + 1;
            } else {
                high = (int) mid - 1;
            }
        }
        return false;
    }
}
```
值得注意的是，我们有使用`mid * mid`的运算。细心的同学会发现，这里存在`integer overflow`的可能
性，并且它会导致无限循环。因此，我们使用了`long`

## Complexity Analysis
- **Time Complexity:** O(log(n)). 我们用二分法在[0,num]范围内寻找了`num`相应的根。
- **Space Complexity:** O(1). 我们没有使用额外的空间。

## 拓展

天才选手牛顿发现可以使用泰勒级数的前面几项，来对实数、虚数范围内方程求近似解，并用来寻找整数根。
详细数学证明见[这里](https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division).
解法如下：
```java
public boolean isPerfectSquare(int num) {
    long x = num;
    while (x * x > num) {
        x = (x + num / x) >> 1;
    }
    return x * x == num;
}
```

## 总结

数学题型一般有两类方法，一类为直观易得的编程思想解（比如二分法等），另一类为数学思想解，对我们有较高
的数学要求，熟悉相应的数学方法。前者需要大量计算机科学、算法的基础；后者需要扎实的数学积累。

## Reference

- [Newton's Method](https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division)
- [fhqplzj's Solutions](https://leetcode.com/problems/valid-perfect-square/discuss/83874/A-square-number-is-1%2B3%2B5%2B7%2B...-JAVA-code)