# [Leetcode 347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

## 题目

Given a non-empty array of integers, return the **k** most frequent elements.

**Example 1:**
```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```
**Example 2:**
```
Input: nums = [1], k = 1
Output: [1]
```

**Note:**
- You may assume *k* is always valid, 1 <= k <= number of unique elements.
- Your algorithm's time complexity must be better than O(nlog(n)), where n is the array size.

## 思路

既然要找到出现频率最高的元素，我们首先需要记录所有元素出现的次数。我们的老朋友HashMap可以帮我们
存储<Number, Freq>这个key-value pair。找到所有频率之后，我们需要在他们中间取出频率最高的k个。

我们自然可以把所有元素放入PriorityQueue，以他们的频率从高到低排序。但是每次PriorityQueue的
插入操作需要O(log(n))的时间，这样总共的时间复杂度就达到了O(nlog(n))，不满足题目要求。

我们可以做一个简单的调整。我们只在PriorityQueue里面保存k个元素，如果插入新元素让PriorityQueue的
大小超过了k，我们则把当前出现频率最低的丢出来。

## 解答
```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        
        // Count numbers
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int i : nums) map.put(i, map.getOrDefault(i, 0) + 1);
        
        // Priority queue that less frequent get poll first
        PriorityQueue<Integer> pq = new PriorityQueue<Integer>((n1, n2) -> map.get(n1) - map.get(n2));
        for (Integer n : map.keySet()) {
            pq.offer(n);
            if (pq.size() > k) 
                pq.poll(); // maintain only k most frequent
        }
        
        // Formulate result
        List<Integer> res = new ArrayList<Integer>();
        while (! pq.isEmpty()) 
            res.add(pq.poll());
        Collections.reverse(res);
        return res;
    }
}
```

## Complexity Analysis
- **Time Complexity:** O(nlog(k)). 用HashMap记录全部元素频率需要O(n)，将n个元素放入大小为k的PriorityQueu排序需要O(nlog(k)).

- **Space Complexity:** O(n). 我们使用了额外O(n)的空间来使用HashMap和PriorityQueue。 

## 拓展

在上面的解法中我们使用了PriorityQueue来保持前k个的顺序，但是让时间复杂度增加到了O(nlog(k)).
我们如何优化以避免使用PriorityQuue呢？我们可以所使用BucketSort来解决这个问题吗？

## 总结

解答这类题型我们需要对各种数据结构的特征、复杂度和用法非常熟悉，比如计数能想到用HashMap，排序想到用
PriorityQueue。

## Reference

- [Leetcode Official Solution](https://leetcode.com/problems/top-k-frequent-elements/solution/)