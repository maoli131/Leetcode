# [Leetcode 227. Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)

## 题目

Implement a basic calculator to evaluate a simple expression string. The expression string contains only **non-negative** integers, `+`, `-`, `*`, `/` operators and empty spaces ` `. The integer division should truncate to zero. 

**Example 1:**
```
Input: "3+2*2"
Output: 7
```

**Example 2:**
```
Input: " 3/2 "
Output: 1
```

**Example 3:**
```
Input: " 3+5 / 2 "
Output: 5
```

**Note:**
- You may assume that the given expression is always valid.
- **Do not** use the `eval` built-in library function.

## 思路

这道题目类似于224题，不过不再需要考虑括号嵌套，而仅需注意加减乘除的优先级。我们还是可以借助stack的思想，将数字和符号都存进去，依次运算。不过，因为这里需要实现乘除优先于加减，我们可以在第一遍遍历把数/符号放入stack的时候，把所有乘除的结果全部运算出来，再放入stack；完成之后，第二遍遍历遍历stack中剩余的数，进行加减运算。

其他的过程类似于224。首先，我们需要循环连续digit character来构造数字（如string "123" 按char遍历得到 '1' * 100 + '2' * 20 + 3）。当每次遇见新的operator时，我们知道当前的数字即operand已经构造完成，可以将它与之前结果进行上一次operator的运算。举个例子，当input string为"12 + 34 - 5", 我们刚好遍历至'4'这个char，stack为[12], `previous operator`为+号，`operand`为34，当我们读到`-`号时，我们知道当前`operand` 34已经构造完成，我们可以将它和stack中第一个数12进行`previous operator`的加法运算。（详见解答）

值得注意的是，这道题目有很多edge case，比如空格出现在数的不同位置、仅单一数出现而没有`operator`等等，大家都需要更仔细的考虑，设置条件判断。

## 解答
```java
class Solution {
    public int calculate(String s) {
        
        Stack<Integer> stack = new Stack<Integer>();
        int operand = 0;
        int prev_op = '+';
        
        for (int i = 0; i < s.length(); i++) {
            
            char ch = s.charAt(i);
            
            // Covert digits to number (e.g. operand = 12, string = "123" -> 12 * 10 + 3)
            if (Character.isDigit(ch)) {
                operand = operand * 10 + Character.getNumericValue(ch);
            }
            
            // Meet new operator or end of string: end of current operand
            if ( (!Character.isDigit(ch) && ch != ' ') || i == s.length() - 1) {
                switch(prev_op) {
                    // additon and subtraction only stores the values for later uses
                    case '+': 
                        stack.push(operand);
                        break;
                    case '-':
                        stack.push(operand * -1);
                        break;
                    // perform the multiplication and division right now and store result
                    case '*':
                        stack.push(stack.pop() * operand);
                        break;
                    case '/':
                        stack.push(stack.pop() / operand);
                        break;
                }
                operand = 0;
                prev_op = ch;
            }
        }
        
        // Sum up all remaining numbers in stack.
        int res = 0;
        for (Integer num : stack) {
            res += num;
        }
        return res;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). 我们需要遍历两次`input string`中的数。

- **Space Complexity:** O(n). 在最坏的情况下，我们需要用stack来储存所有的数

## 拓展

我们能只遍历一次`input string`而完成所有的运算吗？如何做到？不行的话，我们还有其他方式可以优化这个算法吗？

## 总结

对于这类算式运算题，我们必须熟悉数字构建、stack运用这两个基础building block. 基于此，我们需根据题目要求（如括号优先，或乘除优先）来设计相应的进stack、出stack逻辑，以满足题意。同时，我们要足够仔细，分别不同的edge case，充分测试代码。

## Reference

- [abner's solution](https://leetcode.com/problems/basic-calculator-ii/discuss/63003/Share-my-java-solution)