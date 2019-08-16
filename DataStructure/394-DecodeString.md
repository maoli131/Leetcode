# [Leetcode 394. Decode String](https://leetcode.com/problems/decode-string/submissions/)

## 题目

Given an encoded string, return its decoded string.

The encoding rule is: `k[encoded_string]`, where the *encoded_string* inside the square brackets is being repeated exactly *k* times. Note that *k* is guaranteed to be a positive integer. 

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, *k*. For example, there won't be input like `3a` or `2[4]`.

**Examples:**
```
s = "3[a]2[bc]", return "aaabcbc".
s = "3[a2[c]]", return "accaccacc".
s = "2[abc]3[cd]ef", return "abcabccdcdcdef".
```

## 思路

这道题目与之前两道224和227 Basic Calculator非常类似，我们需要借助两个Stack来完成string evaluation，只不过规则稍有不同。首先，我们还是需要遇见digit char的时候用循环的方式构造multipler k。当遇见`'['`时，我们知道目前的multiplier已经构造完成，因此我们可以将它放进`kstack`中保存起来。当遇见`']'`时，我们知道当前括号中的`str`已经构造完成，我们可以依据`kstack`中的multipler k来将`str`重复k次。

这题比较困难的在于处理两个`'['`中间的值，比如`2[a3[b]]`里面的a。为了能让`3[b]`所展开的string正确地接着a，我们需要将a保存在strstack中（这类似于计算器题目中的保存中间结果的stack）。我们可以将`strstack`理解为保存括号层级的一种方式，每往下加深一次括号嵌套，我们就会先往`strstack`中保存当前层级的中间`str`结果。

## 解答
```java
class Solution {
    public String decodeString(String s) {
        
        Stack<Integer> kstack = new Stack<Integer>(); // stores the multiplier
        Stack<String> strstack = new Stack<String>(); // stores all intermediate str
        String str = ""; // result string
        int k = 0; // multipler before each number
        
        for (char ch : s.toCharArray()) {
            
            // Construct the multipler k
            if (Character.isDigit(ch)) {
                k = k * 10 + Character.getNumericValue(ch);
                continue;
            }
            
            switch(ch) {
                
                case '[': // end of multipler
                    kstack.push(k);
                    strstack.push(str);
                    k = 0;
                    str = "";
                    break;
                
                case ']': // end of current str
                    int repeatedTimes = kstack.pop();
                    StringBuilder prev_res = new StringBuilder(strstack.pop());
                    for (int i = 0; i < repeatedTimes; i++) {
                        prev_res.append(str);
                    }
                    str = prev_res.toString();
                    break;
                    
                default:  // normal char
                    str += ch;
                    break;
            }
        }
        
        return str;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(nk). 我们会遍历`input string`中的所有字母，不过重复sub string的时候平均会重复k次。

- **Space Complexity:** O(n). 我们需要用两个`stack`来分别存储k值和中间字符串结果。


## 拓展

这道题能通过递归来解答吗？与循环解答相比，时间空间复杂度是多少？

## 总结

这道题与两道Calculator的题本质上是一样的：我们需要借助stack保存中间结果、调整运算顺序。在解答这些题时，我们需要格外小心处理各种edge case，比如这道题目中重置str字符串、push to stack的位置和条件。

## Reference

- [sampsonchan's solution](https://leetcode.com/problems/decode-string/discuss/87534/Simple-Java-Solution-using-Stack)