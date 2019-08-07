# [Leetcode 253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

## 题目

Given an array of meeting time intervals consisting of start and end times
`[[s1, e1], [s2,e2]]`, (s_i < e_i), find the minimum number of conference rooms 
requried.

**Example 1:**
```
Input: [[0, 30],[5, 10],[15, 20]]
Output: 2
```
**Example 2:**
```
Input: [[7,10],[2,4]]
Output: 1
```

## 思路

题目初见会让人不太有头绪，我们不妨联系实际看看我们在真实世界中怎么解决这个问题的。假设我们在某一
时间准备使用会议室，没有预定的情况下，我们只好挨个查看每个会议室有没有人、有人的话开会到几点。如果
我们发现有空的会议室（即之前使用这间会议室的人已经在我们来之前散会了），我们则可以占用这个房间；
如果没有空会议室，我们则只好等最早散会的那个房间结束，或者凭空创造新的房间。

以上的场景或许能给我们一些启发。首先，会议室都是先到先得的，有空房间、有人来到，无论他们可能会占
用多久，房间都会给他们。既然如此，我们可以将input的`interval`按照`start`时间来进行排序。然后，
我们是依据正在举行会议们的结束时间，即是否在我们之前结束，来判断是否有现成的房间可用。如果没有，
我们则需要创造新的房间。显然，我们只需要关注最早结束的会议时间，即当前所有`intervals`最早的`end`时间。

我们把以上的思想转化为算法。排序比较简单。而判断当前最早结束的会议时间，”最早“又提示我们使用老朋友
MinHeap。第一个会议肯定会占用至少一个房间，我们把它的`end`放入我们的MinHeap中；对于接下来的每一个
会议，我们去比较其`start`时间和`MinHeap.peek()`所代表的的当前最早会议结束时间，以判断我们能否使用
现有房间，如果能，我们则用这个新的`end`时间代替原有的；如果不能，我们则直接放入新的`end`。

这样一来，MinHeap的大小即为我们所使用房间的个数。

## 解答
```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        
        if (intervals.length == 0) return 0;
        
        // Sort by the start time
        Arrays.sort(intervals, (n1, n2) -> n1[0] - n2[0]);
        
        PriorityQueue<Integer> endTimes = new PriorityQueue<Integer>();
        endTimes.offer(intervals[0][1]);
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] >= endTimes.peek()) { // existing room available
                endTimes.poll();
            }
            endTimes.offer(intervals[i][1]); // add new endTime (room)
        }
        
        return endTimes.size();
        
    }
}
```
## Complexity Analysis

- **Time Complexity:** O(nlog(n)). 排序需要占用O(nlog(n))的时间；每次Heap的操作需要O(log(n))时间，而总共可能有n次，即O(nlog(n)).

- **Space Complexity:** O(n). 我们需要储存会议的`endtime`与Heap中。

## 拓展

- 如果不使用PriorityQueue而仅仅使用两个Pointer你能解决这个问题吗？

## 总结

对于这种区间类的题目，首先可以尝试的就是排序，因为排序能为我们提供可为后面所有的时间顺序。然后我们需要根据题目意思，推理如何
设计判断条件，比如`endTime`的比较规则，来做出解答。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/meeting-rooms-ii/solution/)