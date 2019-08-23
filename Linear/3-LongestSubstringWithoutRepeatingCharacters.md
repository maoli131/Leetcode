# [Leetcode 3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

## 题目

Given a string, find the length of the **longest substring** without repeating characters.

**Example 1:**
```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```
**Example 2:**
```
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```
**Example 3:**
```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

## 思路

这道题目跟438很像，但是更简单直接一些。我们需要做两件事情：一是找到所有没有重复字母的substring，二是找到他们中最长的长度。如果我们可以完成一，那么二自然是迎刃而解。为了解决一，我们可以采用sliding window的思想，用一快一慢两个指针遍历string中所有的字母。在寻找non-repeating character substring时，慢指针固定指向substring的头部，快指针不断向前移动直到发现重复字母。如果发现重复字母，则保持快指针不变，向前移动慢指针，直到快指针位置不再为重复字母。为了记录字母是否重复，我们可以利用HashSet。

用以上的双指针sliding window方法我们能O(n)时间遍历所有的substring。这时，我们只需要在每次找到non-repeating substring时去检查它的长度是否比global maxLength大即可。

## 解答
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        
        int maxLength = 0;          // global maximum length
        HashSet<Character> hashSet = new HashSet<Character>(); // check the reapting chars
        
        int slow = 0; int fast = 0; // two pointers
        while (slow < s.length() - maxLength && fast < s.length()) {
            if (! hashSet.contains(s.charAt(fast))) {    // non-repeating: move fast forward
                hashSet.add(s.charAt(fast));
                fast++;
            }
            else {     // repeating: update max and move slow pointer
                hashSet.remove(s.charAt(slow));
                maxLength = Math.max(maxLength, fast - slow);
                slow++;
            }
        }
        return Math.max(maxLength, count);
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). 最坏情况下慢指正会从头遍历到最后一个字母。

- **Space Complexity:** O(n). 最坏情况下HashSet需要保存整段String的所有字母。

## 拓展

这道题目非常经典，可以被拓展成多种不同的问题。这里要求找到non-repeating char string，我们可以改为anagrams, reverse string, substring with at most k distinct chars等等。

## 总结

双指针问题有一下几点核心需要我们在解题的时候分析：
- 双指针的移动规则：比如什么时候移动快指针，什么时候移动慢指针；
- 双指正的移动方向：同向，对向，共向，以解决不同的问题
