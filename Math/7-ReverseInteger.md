# [Leetcode 7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)

## Question

Given a 32-bit integer, reverse digits of an integer. 

**Example 1:**
```
Input: 123
Output: 321
```
**Example 2**
```
Input: -123
Output: -321
```
**Example 3**
```
Input: 120
Output: 21
```

**Note:**
Assume we are dealing with an environment which could only store integers within 32-bit signed integer range: [-2^31, 2^31 - 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows. 

## Thoughts

The word "reverse" reminds us of stack or queue. However, in this question we don't need an actual data structure, but we can mimick such behaviour with integer operations. 

Given any integer x, we can get its digits from right to left using following "pop" operation. 
```
repeat while x > 0:
    digit = x % 10          // get last digit
    x /= 10                 // remove last digit
```

Since we are able to get those digits in reverse order, we only need a "queue" to store the result. Again, we can use integer operations to mimick "push to back".
```
while we have digits:
    result *= 10                // make space for a new rightmost digit
    result += digit             // push to back the new rightmost digit
```

Then we only need to handle some the edge cases such as integer overflow. We can compare the integer result with Integer.MAX_VALUE / 10, or we can simply use a long type to store intermediate result. I prefer the latter. Nonetheless, do confirm what types you can use in a given context. 

## Solution

This solution can certainly be simplified. No need to use sign or the long type result. Yet, I write it as such to ensure the best readability. Codes are for humans, not machines. 
```java
class Solution {
    public int reverse(int x) {
        
        int sign = (x >= 0) ? 1 : -1;
        
        // get digits in reverse order
        x = Math.abs(x);
        long res = 0;
        while (x > 0) {
            
            res = res * 10 + x % 10; // push to back the peeked value
            x /= 10;                 // pop 
            
            // check overflow
            if (res < Integer.MIN_VALUE || res > Integer.MAX_VALUE) return 0;       

        }
        return (int) res * sign;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). We iterate through every digit of the input integer. n is the number of digits x has, which is approximately log_10_(x). 

- **Space Complexity:** O(1). We don't need any extra space since we ues integer operations to mimick the stack/queue data structure.

## Extension

If the input is of type float, can you reverse it and keep the number of decimals?

## Summary

To solve this type of question, we first consider the underlying core ideas: stack or queue that reverses something. Then we move beyond that to see if we can achieve the same behaviour without wasting extra space. 

As a side note, this is the first Leetcode question I tried two years ago when I was a freshman. Even though I ranked the very top in all three of intro cs courses (programming and data structures), I spent over 45 minutes and still failed twice. Back then I didn't have the fundamental instincts for algorithm questions. Now, after one month of light practice and consistent summary, I began to grasp the hints and core ideas behind these questions. I solved it under 10 mintues and beated 100% in first submission. 

## Reference

- [Mao's](https://www.li-mao.net) original series

