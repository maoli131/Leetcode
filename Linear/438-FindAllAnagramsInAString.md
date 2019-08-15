# [Leetcode 438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

## 题目

Given a string **s** and a **non-empty** string **p**, find all the start indices of **p**'s anagrams in **s**. Strings consists of lowercase English letters only and the length of both strings **s** and **p** will not be larger than 20,100.

The order of output does not matter.

**Example 1;**
```
Input:
s: "cbaebabacd" p: "abc"

Output:
[0, 6]

Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

**Example 2:**
```
Input:
s: "abab" p: "ab"

Output:
[0, 1, 2]

Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
``` 

## 思路

我们先从最简单的情况开始思考：`p`中没有重复的字母。比如`s = cbadeacb, p = abc`. 这时我们可以运用HashSet保存p中的所有字母，用双指针遍历s中所有的substring。这里双指针为：left指向substring的开始，从s的开始一直走到s.length - p.length的位置，以检查s中的所有substring。对于每个left，right从left位置一直跑到left + p.length，以检查s中长度和p相同的substring是否是p的anagram。我们可以设一个`count`变量，以记录在substring中字母是p中字母的次数，如果`count`等于p.length，则说明我们找到了anagram.

当`p`中有重复字母时，情况变得稍微复杂一些了。我们不仅需要记录p中每个字母出现的次数，还得检查在s的substring里面，是不是所有p里字母都出现了与p里相同的次数。前者可以通过HashMap来实现，以记录{char: frequency}。可是后者则较为麻烦。如果我们在检查某个substring的过程中对HashMap进行了修改，那么检查其他substring的时候结果就不会准确。这时我们需要运用`sliding window`的思想，在每次检查完一个substring后，并移动左指针的时，我们应该通过左指针来重置HashMap，以使HashMap正确表示p中字母及其频率。同时，因为right遇见合格字母时才改变`count`，left同样也只应该在遇见合格字母时重置`count`。

## 解答
```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> list = new ArrayList<Integer>();
        if (s == null || s.length() == 0 || p == null || p.length() == 0) return list;
        
        int[] map = new int[256]; // char map {char: freq}
        for (char ch : p.toCharArray()) {
            map[ch]++;
        }
        
        int count = p.length(); 
        int left = 0; int right = 0;
        while (right < s.length() ) {
            
            // find a matching char
            if (map[s.charAt(right)] > 0) count--; 
            map[s.charAt(right)]--;
            right++;
                  
            // find an anagram
            if (count == 0) list.add(left); 
            
            // reset count and map using left pointer 
            if (right - left == p.length()) {
                if (map[s.charAt(left)] >= 0) count++;
                map[s.charAt(left)]++;
                left++;
            }
        }
        return list;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(n). 我们的左右指针会以sliding window的方式各自遍历整个数组。

- **Space Complexity:** O(1). 我们虽然使用了map来储存字母出现次数，但是由于字母个数的有限的（255），这个map只会占用constant space。

## 拓展

除了FindAnagram以外，我们还有很多类似的substring可以用类似的sliding windows的方法来解决。以下为这一大类问题的算法模板：
```C++
int findSubstring(string s){
        vector<int> map(128,0);
        int counter; // check whether the substring is valid
        int begin=0, end=0; //two pointers, one point to tail and one  head
        int d; //the length of substring

        for() { /* initialize the hash map here */ }

        while(end<s.size()){

            if(map[s[end++]]-- ?){  /* modify counter here */ }

            while(/* counter condition */){ 
                 
                 /* update d here if finding minimum*/

                //increase begin to make it invalid/valid again
                
                if(map[s[begin++]]++ ?){ /*modify counter here*/ }
            }  

            /* update d here if finding maximum*/
        }
        return d;
  }
```

## 总结

Substring类问题核心在于如何找到substring、如何在过程中更新两头的指针、以及如何判断substring是否为valid。抓住以上几个要点后，我们则可以应用上面的通用算法思路，去解决细节不同但本质一样的各种substring类题目。

## Reference

- [zjh08177](https://leetcode.com/problems/minimum-window-substring/discuss/26808/here-is-a-10-line-template-that-can-solve-most-substring-problems)
- [chaoyanghe's sliding window template](https://leetcode.com/problems/find-all-anagrams-in-a-string/discuss/92007/Sliding-Window-algorithm-template-to-solve-all-the-Leetcode-substring-search-problem.): Very Helpful.
- [NathanNi's solution](https://leetcode.com/problems/find-all-anagrams-in-a-string/discuss/92015/ShortestConcise-JAVA-O(n)-Sliding-Window-Solution)
