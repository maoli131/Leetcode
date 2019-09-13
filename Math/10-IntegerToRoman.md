# [Leetcode 12. Integer to Roman](https://leetcode.com/problems/integer-to-roman/)

## Problem

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

```
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, two is written as `II` in Roman numeral, just two one's added together. Twelve is written as, `XII`, which is simply `X` + `II`. The number twenty seven is written as `XXVII`, which is `XX` + `V` + `II`.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as `IX`. There are six instances where subtraction is used:

- `I` can be placed before `V` (5) and `X` (10) to make 4 and 9. 
- `X` can be placed before `L` (50) and `C` (100) to make 40 and 90. 
- `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

Given an integer, convert it to a roman numeral. Input is guaranteed to be within the range from 1 to 3999.

**Example 1:**
```
Input: 3
Output: "III"
```

**Example 2:**
```
Input: 4
Output: "IV"
```

**Example 3:**
```
Input: 9
Output: "IX"
```

**Example 4:**
```
Input: 58
Output: "LVIII"
Explanation: L = 50, V = 5, III = 3.
```

**Example 5:**
```
Input: 1994
Output: "MCMXCIV"
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

## Thoughts

As [those highly voted discussion posts](https://leetcode.com/problems/integer-to-roman/discuss/6274/Simple-Solution) had figured out, we can manually write out all ten roman digits for each of the ones, tens, hundreds, and thousands places. In this way we hard-coded 40ish digits and used only one line of "code". Admittedly, these hard coded solutions will work perfectly given that roman numerals only range from 1 to 3999. 

However, I'd like to suggest an alternative solution that is more **general** and **extensible**. Assume some day magically we needs roman numerals to include a very large range of number (e.g. 1 - 99999) and we adds more symbols like "Y, Z, .." besides "X, L, C, D, M". Then it's obvious we shoudn't keep hard coding. That's not what we as programmers do. 

Instead, we should optimally only code the **RULES** that converts integer to roman numerals, as following:
- the conversion table: symbols (arbitrary large number) to values, e.g. X to 10 or C to 100. 
- general rule: largest to smallest from left to right, by adding symbols together.
- exception: 4 and 9 case.

Besdies above RULES, the digit by digit conversion should be AUTOMATICALLY performed by computer. 

## Solution

This solution enables us to easily present **arbitarily large range of number** in "roman" numerals with new symbols. Instead of hard coding every digits, we only need to include new symbols in the lookup table. (My code should be further improved to be more concise). 

In a more pratical sense, it's always good to show interviewers that you have a mindset for extensibility and maintainability.

```java
class Solution {
    public String intToRoman(int num) {
        
        // Roman lookup table
        char[][] roman = {
            {'I', 'V'},
            {'X', 'L'},
            {'C', 'D'},
            {'M'}
        };
        
        StringBuilder res = new StringBuilder();
        int power = 0;
        final int ONE = 0; final int FIVE = 1;
        
        while (num > 0) {    
            int digit = num % 10;
            
            // Two exceptions of 4 and 9
            if (digit == 4) {
                res.insert(0, roman[power][FIVE]);
                res.insert(0, roman[power][ONE]);
            }
            
            else if (digit == 9) {
                res.insert(0, roman[power + 1][ONE]);
                res.insert(0, roman[power][ONE]);
            }
            
            // General rule for digit larger than 5
            else if (digit >= 5) {
                for (int i = 0; i < digit - 5; i++) {
                    res.insert(0, roman[power][ONE]);
                }
                res.insert(0, roman[power][FIVE]);
            }
            
            else { // digit < 5 and not four
                for (int i = 0; i < digit; i++) {
                    res.insert(0, roman[power][ONE]);
                }
            }
            
            num /= 10;
            power += 1;
        }
        
        return res.toString();
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(k), where k equals to the number of digits input number have. We have to iterate all digits of the input number. 

- **Space Complexity:** O(1). No additional space is used except the output string builder.

## Extension

Simplify the code to make it more readable and concise.

## Summary

This question can surely be solved efficiently using brutal force. It's fast and it beats 100%. Nonetheless, these solutions lack the essense of programmic thinking. When we design an algorithm, we should not only care about solving it under given context, but also establishes foundations for future extension if requirement changes. 

## Reference

- [M's post on the discussion section](https://leetcode.com/problems/integer-to-roman/discuss/377063/A-more-GENERAL-and-EXTENSIBLE-Java-solution)
