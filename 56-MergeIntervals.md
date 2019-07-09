# [LeetCode 56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

## 题目

Given a collection of intervals, merge all overlapping intervals.

**Example 1:**
```
Input: [[1,3], [2,6], [8, 10], [15, 18]]
Output: [[1, 6], [8, 10], [15, 18]]
Explanation: Since intervals [1, 3] and [2, 6] overlaps, merge them into [1, 6].
```

**Example 2:**
```
Input: [[1, 4], [4,5]]
Output: [[1,5]]
Explanation: Intervals [1, 4] and [4, 5] are considered overlapping.
```

## 思路

类似于其他线性结构的题目，我们可以尝试**排序+双指针**的解题思路。排序是为了方便我们更高效地**找到重合区间**，双指针是为了让我们**合并重合区间**。

首先，在我们把所有的Interval都根据`start`排序之后，可能重合的区间必然是连续排列的。然后，我们将头指针指向某个Interval，用尾指针扫描与之连续的Interval来检查他们是否重合。
- 如果重合，则将他们合并，即将头指针Interval的`end`更新为合并后Interval的`end`，并继续检查之后连续的Interval；
- 如果不重合，说明我们已经完成了头指针Interval的重合合并，我们可以直接把头指针Interval放入`merged result`。

## 解答
``` java
class Solution {
   
    public int[][] merge(int[][] intervals) {
        
        // Edge case: one or no interval
        //  We consider this edge case because we have to point to 
        //  at least one interval in later code
        if (intervals.length <= 1) return intervals;

        // Sort by ascending order based on start (Java 8 syntax)
        Arrays.sort(intervals, (a, b) -> Double.compare(a[0], b[0]));

        // Scan through consecutive intervals and merge them
        ArrayList<int[]> merged = new ArrayList<int[]>();
        merged.add(intervals[0]);
        int[] curr = intervals[0];

        for (int[] next : intervals) {
            if (curr[1] >= next[0]) { // Merge
                curr[1] = Math.max(curr[1], next[1]);
            }
            else { // Non-overlapping
                curr = next;
                merged.add(curr);
            }
        }

        // Convert back to 2-d array
        int[][] res = new int[merged.size()][2];
        for (int i = 0; i < res.length; i++) {
            res[i] = merged.get(i);
        }
        return res;
    }
}
```

## Complexity Analysis
- **Time Complexity:** 
  $$ 
  O(nlog(n)) 
  $$
  排序需要nlog(n)，合并重合只是linear scan了整个区间数组，所以最终的Time Complexity是O(nlog(n)).

- **Space Complexity:**
  $$
  O(1)
  $$
  虽然我们新建了`merged`来存储合并后的区间，但是它是output所必要的空间，所以我们并不将它计算在Space Complexity之中。因为Java的`Arrays.sort`是in-place sorting，所以我们并不需要额外的空间。因此，Space Complexity为O(1).

## 优化/其他解答

我们可以更进一步深入理解这个问题：我们的解法本质上是对每一个`start`，去匹配它所对应的合并区间之后的`end`。因此，我们可以更进一步，将原有的`Intervals`拆分成两个不同的数组，一个包括所有的`start`，另一个包括所有的`end`。分别排序两者之后，我们可以直接寻找匹配的`start`和`end`，以寻找到合并后的区间。

## 总结
- 排序能够方便我们更高效地找到重合区间；
- 双指针方便我们合并重合区间。