# [Leetcode 295. Find Media From Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)

## 题目

Median is the middle value in an ordered integer list. If the size of the list 
is even, there is no middle value. So the median is the mean of the two middle value.

For example,
`[2,3,4]`, the median is `3`.
`[2,3]`, the median is `(2 + 3) / 2 = 3.5`

Design a data structure that supports the following two operations:
- void addNum(int num) - Add a integer number from the data stream to the data structure.
- double findMedian() - Return the median of all elements so far.

**Example:**
```
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3)
findMedian() -> 2
```

## 思路

这道题目最简单粗暴的解法为对所有数进行排序，返回中点。这样时间复杂度非常高，达到惊人的O(nlog(n)). 
在此基础上我们可以进行优化，既然要排序，那我们可以使用Insertion Sort的思想，即在插入数时将它
放到排序好的位置。这样时间复杂度就被减少到了O(n)。

我们从以上的两种初级方法中发现，这道题目提升速度的核心在于：通过某种方式保持存储元素的顺序，以此
Constant Time返回相应中点。为了在此基础上进一步提升，我们首先回忆中点的定义。
```
中点将数组分为Larger和Smaller两个满足以下条件的部分：
  - 两个部分大小相等
  - Larger中的所有元素比Smaller中所有元素大
```

以此我们可以发现，找中点并不需要保持所有元素的顺序！我们只要保证所有元素能被分为两个数量相等的部分，
且Larger中的最小数 > Smaller中的最大数即可。在这个条件下，中点就是这两个最大最小数中的一个或者
它们的平均值。

最大最小这两个关键词提示我们可以使用Heap（Java中的PriorityQueue）来实现这个算法。我们要保证两个
条件：
1. 两个Heap大小相等
2. 装较小元素们的Heap的最大值 (maxHeap) < 装较大元素们的Heap的最小值 (minHeap)

## 解答
```java
class MedianFinder {
    
    PriorityQueue<Integer> minHeap; // keeps the larger part of elements
    PriorityQueue<Integer> maxHeap; // keeps the smaller part of elements
    
    /** initialize your data structure here. */
    public MedianFinder() {
        minHeap = new PriorityQueue<Integer>();
        maxHeap = new PriorityQueue<Integer>(Collections.reverseOrder());
    }
    
    public void addNum(int num) {
        minHeap.offer(num);
        // smallest of the larger part > biggest of the smaller part
        if (!minHeap.isEmpty() && !maxHeap.isEmpty() && minHeap.peek() < maxHeap.peek()) { 
            Integer temp = minHeap.poll();
            minHeap.offer(maxHeap.poll());
            maxHeap.offer(temp);
        }
         // make sure they are equal in length
        if (minHeap.size() > maxHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if ((minHeap.size() + maxHeap.size()) % 2 == 0) {
            return (minHeap.peek() + maxHeap.peek()) / 2.0;
        }
        return maxHeap.peek();
    }
}
/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

## Complexity Analysis

- **Time Complexity:**
  - addNum(int n)时间复杂度为O(log(n))，每次插入最多有四次Heap offer()即为O(4 * log(n)).
  - findMedian()时间复杂度为O(1). 每次查询只需O(1)的时间拿出两个Heap的最大最小。

- **Space Complexity:** O(n). 我们需要两个额外的Heap来储存这些元素，即为O(n).

## 拓展

- If all integer numbers from the stream are between 0 and 100, how would you optimize it?
- If 99% of all integer numbers from the stream are between 0 and 100, how would you optimize it?
- 除了用Heap之外，我们还可以使用其他的数据结构比如AVL树来实现这个算法吗？
- 在Leetcode论坛上有讨论多种其他方法，比如Buckets, Reservoir Sampling等等，同学么可以去Reference Link中看看。

## 总结

一如既往，最难的还是透过问题看本质。类似那道经典的Median of Two Sorted List，我们要深刻理解中点的定义，
并以此启发性地灵活运用数据结构来解决问题。

## Reference

[Leetcode Official Solution](https://leetcode.com/problems/find-median-from-data-stream/solution/): 多种方法的全面总结