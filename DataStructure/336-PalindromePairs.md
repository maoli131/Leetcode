# [Leetcode 336. Palindrome Pairs](https://leetcode.com/problems/palindrome-pairs/)

## 题目

Given a list of **unique** words, find all pairs of ***distinct*** indices `[i,j]` in the given list, so that the concatenation of the two words, i.e. `words[i] + word[j]` is a palindrome.

**Example 1:**
```
Input: ["abcd","dcba","lls","s","sssll"]
Output: [[0,1],[1,0],[3,2],[2,4]] 
Explanation: The palindromes are ["dcbaabcd","abcddcba","slls","llssssll"]
```
**Example 2:**
```
Input: ["bat","tab","cat"]
Output: [[0,1],[1,0]] 
Explanation: The palindromes are ["battab","tabbat"]
```

## 思路

读完题之后我们会发现最直接的方法为用O(n^2)的方法组合所有words[]中字符串，并挨个用O(n)的时间判断所有组合是否为panlindrome。显然，我们需要优化这种解法。

首先，我们注意到，如果words[]中存在str和reserve(str)，比如"abc"和"cba"，那么{srt}{reverse(srt)}和{reverse(srt)}{str}即为满足条件的panlindrome。在此基础上，我们可以可以继续思考如何找到长度不一的panlindrome pairs。比如"sssll"和"lls"可以组合成为"llssssll"这样的pandlindrom。仔细观察后我们发现"llssssll" = "lls ss sll"，即"lls {ss | reverse(lls)}"。也就是说，如果我们能将"sssll"拆分为两个部分，一部分为panlindrome（ss)，一部分为word[]中另一个string的reverse，那么我们就找到了满足条件的pair。

也就是说，如果我们可以将一个string拆分为两个部分 str = {str1 | srt2}，我们可以用如下性质：
```
- {reverse(str2) | panlindromic str1 | str2} is a palindrome
- {str1 | panlindromic str2 | reverse(str1)} is a palindrome
```
因此，要找到所有满足条件的pairs，我们可以用HashMap来储存我们见过的所有String，然后将每个word拆分为两部分，判断是否其中一个部分为panlindrom且另一个部分的reverse存在于map中。

## 解答

需要注意的是，我们j的条件为`j = 0; j <= word[i].length()`。从0开始是因为我们要覆盖空String的情况，至word[i].length()是因为substring是左包右开，即substring(0, length)取得恰好是0 ~ length - 1位置的字母。

```java
class Solution {
    public List<List<Integer>> palindromePairs(String[] words) {
        
        List<List<Integer>> res = new ArrayList<>();
        if (words == null || words.length <= 1) return res;
        
        // Store {String: index} into map
        HashMap<String, Integer> map = new HashMap<String, Integer>();
        for (int i = 0; i < words.length; i++) map.put(words[i], i); // O(n)
        
        for (int i = 0; i < words.length; i++) { // O(n)
            for (int j = 0; j <= words[i].length(); j++) { // O(k) to iterate each string
                
                // split string into two parts
                String str1 = words[i].substring(0, j);
                String str2 = words[i].substring(j);
                
                // reverse(str2) + panlindromic str1 + str2 is a palindrome 
                if (isPanlindrome(str1)) { // O(k) to check palindrome and reverse
                    String str2rev = reverse(str2);
                    if (map.containsKey(str2rev) && map.get(str2rev) != i)
                        res.add(Arrays.asList(map.get(str2rev), i));
                }
                
                // str1 + panlindromic str2 + reverse(str1) is a palindrome
                if (isPanlindrome(str2)) { // O(k) to check palindrome and reverse
                    String str1rev = reverse(str1);
                    if (map.containsKey(str1rev) && map.get(str1rev) != i && str2.length() != 0)
                        res.add(Arrays.asList(i, map.get(str1rev)));
                }
                
            }
        }
        
        return res;
    }
    
    // O(k) time to reverse the string
    private String reverse(String str) {
        StringBuilder sb = new StringBuilder(str);
        return sb.reverse().toString();
    }
    
    // O(k) time to check whether isPanlindrome
    private boolean isPanlindrome(String str) {
        if (str == null || str.length() == 0) return true;
        int left = 0; int right = str.length() - 1;
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) return false;
            left++;
            right--;
        }
        return true;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n*k^2). 我们需要用O(n)的时间遍历所有的字符串，然后对于每个字符串（平均长度假设为k），我们需要将其拆分k次，且对每一个此拆分，我们都需要用再用O(k)的时间检查其是否为panlindrome、翻转不是panlindrome的部分。因此，总时间复杂度为O(n * k^2)

- **Space Complexity:** O(n). 我们需要使用额外的HashMap储存所有的String。如果我们考虑每次中间拆分过程中创造的String所占用空间，则复杂度为O(nk).

## 拓展

这道题目还有一种时间复杂度相同但运行速度更快的解法，需要我们自己实现并使用Trie这个数据结构。这种解法稍微复杂一些，理解起来也更有挑战性，实现起来没有以上解法那么直观，但是它也是非常有用的练习，感兴趣的同学可以从[这里](https://leetcode.com/problems/palindrome-pairs/discuss/79195/O(n-*-k2)-java-solution-with-Trie-structure)了解。

## 总结

这道题的解法需要我们耐心细致的从最基础的两个reverse string的情况出发，再通过细致的观察更复杂的不同的情况，以总结出规律。同时，在实现代码时，也需要仔细确保所有的edge case已经被处理完，循环条件等不要出错。

## Reference

- [mdhu's solution](https://leetcode.com/problems/palindrome-pairs/discuss/79199/150-ms-45-lines-JAVA-solution)