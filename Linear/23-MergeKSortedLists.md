# [Leetcode 23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

## 题目

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

**Example:**
```
Input:
[
  1->4->5,
  1->3->4,
  2->6
]
Output: 1->1->2->3->4->4->5->6
```

## 思路

这道题目最简单直接的解法为将k个list中所有的元素拿出来放入List中，并对这个大的List进行排序。但是，这种方法时间复杂度达到了O(nlog(n))，
而且我们完全没有巧妙利用**sorted**这个条件。

结合每个list已经被排好序这个特点，我们可知前k个元素（每个list的head）为所有元素中相对较小的k个，且所有元素中的最小元素必在此k个之中。由此，我们
可以比较这k个元素，并把最小的拿出来，作为我们返回列表的第一个元素。值得注意的是，第二小的元素不一定出现在剩下的k-1元素中。它可能仅仅比刚才第一小的元素大，
但是比刚才k-1个元素要小。因此，我们需要把最小元素所在列表的下一个元素拿出来，和剩下的k-1个元素比较，选出最小的，作为我们返回列表的第二个元素。我们
会重复这个过程，直至把所有元素全部排好顺序。

如何在k个元素中找到最小的呢？我们可以使用PriorityQueue，它在任意时间仅储存至多k个元素。

## 解答
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {

        PriorityQueue<ListNode> pq = new PriorityQueue<ListNode>((n1, n2) -> n1.val - n2.val);
        
        // Put all k-smallest heads into pq
        for (ListNode node : lists) {
            if (node != null) pq.offer(node);
        }
        
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (! pq.isEmpty()) { // store at most k elements
            
            // Insert the curr-smallest to the list
            ListNode n = pq.poll();
            curr.next = new ListNode(n.val);
            curr = curr.next;
                
            // Add new node into pq to keep pq's size as k
            if (n.next != null) pq.offer(n.next);
        }
        
        return dummy.next;
    }
}
```

## Complexity Analysis

- **Time Complexity:** O(nlog(k)). PriorityQueue最多有k个元素，因此插入需要O(log(k))的时间。我们都需要把每个元素装进PriorityQueue，因此总共需要O(nlog(k)).

- **Space Complexity:** O(k). PriorityQueue占用额外空间储存k个元素。

## 拓展

我们之间做过Merge 2 Sorted List这道题目。我们能把它的思想运用到这道题目吗？需要做哪些改变？
显然不能直接进行(k - 1)次merge 2 sorted list，我们需要使用Divide and Conquer的思想。