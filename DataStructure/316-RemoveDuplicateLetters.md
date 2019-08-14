# [Leetcode 316. Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters)

## 题目

Given a string which contains only lowercase letters, remove duplicate letters so that every letter appear once
and only once. You must make sure your result is the smallest in lexicographical order among all possible results.

**Example 1:**
```
Input: "bcabc"
Output: "abc"
```
**Example 2:**
```
Input: "cbacdcbc"
Output: "acdb"
```

## 思路

这道题目初见感觉难度很大，因为需要同时考虑：重复、字母位置，以及字母顺序优先级（lexicographical order必须最小）这三个变量。字母表顺序靠前的字母
应该更优先放在前面的位置；字母表顺序靠后的字母应该尽量放在后面的位置。但是应该怎么实现这样的算法呢？

我们可以先从最基础的去重开始，再一步步思考。为了去重，我们需要记录每次出现的字母之前有没有见过；这步骤可以通过`boolean[] visited`来实现，而array的长度
应该刚好为字母个数26. As a bonus，我们可以在这个过程中记录每个字母出现的次数；记录`int[] count`。

完成以上的准备后，为了按照lexicographical从小排序，直观来讲，我们得在每遇见一个新的字母时候判断我们要不要将这个新字母在此时、此地放入我们的结果string。
我们需要做两件事：一是将此字母与当前结果string中其他字母比较，如果比它们小，则应该尽可能放在它们前面；二是判断次字母是否为最后一次出现，如果是，则必须放入
结果string。

通过算法实现以上的思想需要我们创造性的想到`stack`这个数据结构，并对最直观的逻辑进行一些修改。我们可以用一个`stack`来记录当前的结果string中字母。
然后，为了实现第一件事“把小的字母放在前面”，我们可以将新遇字母`newChar`与当前`stack`中字母们比较，如果`newChar`更小，我们**不仅**要把`newChar`放入
`stack`中，**还需要**将当前`stack`中比`newChar`大、且后面会再次出现（这里count就能排上用场了）的字母拿出来。而如果`newChar`更大，我们并不能直接不将它放入`stack`，因为我们无法预测之后的字母是比它大还是小，如果都比它大，那我们就亏了。因此，我们还是得将它先存入`stack`，让它的去留由后人评判。

以上我们就得出了算法的雏形。不过值得注意的是，为了避免重复，我们一旦将某个元素放入`stack`后，我们需设置visted[]，放置重复元素进入。

## 解答
```java
class Solution {
    public String removeDuplicateLetters(String s) {
        
        // Initialize count and visited array
        int[] count = new int[26];
        boolean[] visited = new boolean[26];
        char[] arr = s.toCharArray();
        for (char ch : arr) {
            count[ch - 'a']++;
        }
        
        // Stack to place char at proper position
        Stack<Character> stack = new Stack<Character>();
        for (char ch : arr) { 
            
            count[ch - 'a']--;
            if (visited[ch - 'a'] == true) continue;
            
            // Current peek should be popped, because ch is smaller and curr appears later
            while (!stack.isEmpty() && stack.peek() > ch && count[stack.peek() - 'a'] > 0) {
                visited[stack.peek() - 'a'] = false;
                stack.pop();
            }
            
            visited[ch - 'a'] = true;
            stack.push(ch);
        }
        
        // Build up result array
        StringBuilder sb = new StringBuilder();
        for (char ch : stack) {
            sb.append(ch);
        }
        return sb.toString();
    }
}
```

## Complexity Analysis:

- **Time Complexity:** O(n). 我们需要遍历整个input string中的所有字母。虽然对每个字母我们都有可能持续从stack中循环pop元素，但是因为stack的最大大小为26，因此最终的时间复杂度不是O(n^2)而是O(n).

- **Space Complexity:** O(1). 我们需要使用两个大小为26的数组和一个大小为26的stack。占用空间不取决于input大小。

## 拓展

如果用queue，而不是stack我们能解决这个问题吗？如果能的话，应该怎么调整算法？如果不能的话，queue的什么特性阻碍了解答？

## 总结

这道题目需要将一些非常有创造力的思考和对数据结构的灵活运用结合在一起。毫无头绪的时候还是可以从简单的基础开始，慢慢推导。
