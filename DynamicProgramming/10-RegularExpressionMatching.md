# [Leetcode 10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)

## Question 

Given an input string (`s`) and a pattern (`p`), implement the regular expression matching with support for `'.'` and `'*'`. 

```
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
```

The matching should cover the **entire** input string (not partial).

**Note**
- `s` could be empty and contains only lowercase letters `a-z`.
- `p` could be empty and contains only lowercase letters `a-z`, and characters like `.` or `*`. 

**Example 1:**
```
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

**Example 2:**
```
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

**Example 3:**
```
Input:
s = "ab"
p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

**Example 4:**
```
Input:
s = "aab"
p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore, it matches "aab".
```

**Example 5:**
```
Input:
s = "mississippi"
p = "mis*is*p*."
Output: false
```

## Thoughts

This is a classical dynamic programming question. However, solutions for DP questions are usually not obvious. Our best strategy is to first come up with a recursive solution and then derive the DP version from it.

In order to check if `s` and `p` matches, we only need to check whether their first characters match (or `p` has a wildcard) and then recursive run the algorithms on their substring starting at index 1. Yet, we have to be aware that Kleene stars slightly complicates the problem by adding two following cases, before we are able to recurse.

If the first character of `p` is followed by a `*`, then `s` and `p` matches if either one of the following is true:
1. `s` and `p`'s first character matches AND `s`'s substring starting at index 1 also matches the original pattern `p`. (keep expanding the first matching character of pattern).
2. `s` and `p`'s first character doesn't match AND `s` matches the pattern `p`'s substring starting at index 2. (skip the first two unmatched pattern characters).

Then we are able to arrive at our recursive algorithm as following.

## Solution

Recursive algorithm:
```java
class Solution {
    public boolean isMatch(String s, String p) {
        
        // Base case:
        if (p.length() == 0) return s.length() == 0;
        
        // Recurive case:
        boolean firstMatch = (s.length() != 0 && (p.charAt(0) == s.charAt(0) || p.charAt(0) == '.'));
        if (p.length() > 1 && p.charAt(1) == '*') {
            return isMatch(s, p.substring(2)) || (firstMatch && isMatch(s.substring(1), p));
        }
        else {
            return firstMatch && isMatch(s.substring(1), p.substring(1));
        }
    }
```

Personally I would prefer the more clear and intuitive recursive solution. Nonetheless, DP is also very important and gradually becomes a standard in technical interview. It's also true that the more practice you have, the more familiar and intuitive DP becomes. 

## Complexity Analysis

Recurisve Algorithm: 
- TODO

Dynamic Programming Algorithm:
- **Time Complexity:** O(n^2), where n is the length of input and patten string. We traverse through every cell of the 2 dimensional DP matrix.

- **Space Complexity:** O(n^2). The 2 dimensional DP matrix stores n^2 intermediate results.

## Extension

Mathematically prove and compare recursion and DP algorithm runtime. 

## Summary

Dynamic Programming solution is usually hard to think of, especially when under stress. Yet, we can solve the problem first using recursion, which is much more intuitive, and then check if we can apply dynamic programming. When constructing the DP arrays, we have to think carefully about which intermediate results to store and how can we utilize them to reduce runtime. 

## Reference

- [Leetcode's official solution](https://leetcode.com/problems/regular-expression-matching/solution/)