# [Leetcode 4. Median of Two Sorted Array](https://leetcode.com/problems/median-of-two-sorted-arrays/)

## 题目

There are two sorted arrays **nums1** and **nums2** of size x and y respectively.

Find the median of the two sorted arrays. The overall run time complexity should 
be O(log(x + y)). 

You may assume **nums1** and **nums2** can not be both empty.

**Example 1:**
```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```
**Example 2:**
```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

## 思路

要解决这个问题，我们需要先理解“中位数有什么用？”。在统计学中，**中位数用于将集合划分为两个相等长度的子集，一个子集总是大于另一个子集**。如果我们理解了中位数对集合的划分，我们就非常接近答案了。

首先，在一个随机的位置`paritionX`将集合`nums1`划分为两部分。
```
                       leftNums1             |           rightNums1
nums1[0], nums1[1], ..., nums1[partitionX-1]  |  nums1[partitionX], nums1[partitionX+1], ..., nums1[x-1]
```
由于`nums1`有`x`个元素，所以就有`x+1`种分法(`partitionX=0~x`)。由此可知： `len(leftNums1) = partitionX, len(rightNums1) = x - partitionX`。
注意：当`partitionX = 0`时，`leftNums1`为空，而当`paritionX = x`时，`rightNums1`为空。

同理，在一个随机的位置`paritionY`将长度为`y`的集合`nums2`划分为两部分。
```
                        leftNums2             |           rightNums2
nums2[0], nums2[1], ..., nums2[partitionY-1]  |  nums2[partitionY], nums2[partitionY+1], ..., nums2[y-1]
```

将 leftNums1 和 leftNums2 放入同一个集合，将 rightNums1 和 rightNums2 放入另外一个集合。 分别称他们为 leftPart 和 rightPart：
```
                                      leftPart          |        rightPart
nums1[0], nums1[1], ..., nums1[partitionX-1] (maxLeftX) |  nums1[partitionX] (minRightX), nums1[partitionX+1], ..., nums1[x-1]
nums2[0], nums2[1], ..., nums2[partitionY-1] (maxLeftY) |  nums2[partitionY] (minRightY), nums2[partitionY+1], ..., nums2[y-1]
```

如果我们能达成两个条件：
1. `len(leftPart) = len(rightPart)`
2. `max(leftPart) <= min(rightPart)`
那么我们就能将{nums1, nums2}中所有元素分成两个长度相等的部分，并且其中一部分总是大于另一部分。那么中位数则为：`(max(leftPart) + min(rightPart)) / 2`.

为了达成这两个条件，我们只需确保：
1. `partitionX + partitionY == (x + y + 1) / 2`。所有元素被均匀分成两个部分。
2. `maxLeftX < minRightY && maxLeftY < minRightX`。
   因为所有`leftNums1`一定比`rightNums1`小，满足以上条件的话`leftNums1`则同时也比所有的`rightNums2`小。`nums2`同理。因此，条件二保证了
   `leftPart < rightPart`。

因此，我们需要在[0, x]区间，用二分搜索法找到`partitionX`以满足以上两个不等式。我们可以写出以下的伪代码:
```
<1> 设 low = 0, high = x, 然后在这个区间 [low, high] 中查找 partitionX

<2> 设 partitionX = (low + high)/2, partitionY = (x + y + 1)/2 - partitionX

<3> 此时，我们满足了 len(leftPart)==len(rightPart)， 我们会遇到三种情况：
    <a> maxLeftY <= minRightX and maxLeftX <= minRightY
        说明我们找到了我们需要的paritionX，停止搜索。我们依据nums1和nums2总数判断:
        如果是偶数，leftPart和rightPart数量相等，median = avg(max(maxLeftX, maxLeftY), min(minRightX, minRightY)).
        如果是奇数，leftPart多一个元素，则median = avg(maxLeftX, maxLeftY)
    <b> maxLeftY > minRightX
        意味着 minRightX 太小， 那么我们必须调整 partitionX 以使 `maxLeftY <= minRightX` 仍然成立。
        我们可以增大 i吗?
            Yes. 因为 partitionX 增大时， partitionY 将减小。
            所以 maxLeftY 跟着减小而 minRightX 会增大。maxLeftY <= minRightX就可能成立。
        我们可以减小 partitionX 吗?
            No!  因为 partitionX 减小时， partitionY 将增大。
            所以 maxLeftY 增大而 minRightX 减小。`maxLeftY <= minRightX` 永远不可能成立。
        所以我们必须增加partitionX。也就是将搜索范围调整为[partitionX+1, high]。 所以，设 low = partitionX+1, 然后回到步骤 <2>.
    <c> maxLeftX > minRightY
        意味着 maxLeftX 太大。我们必须减小 partitionX 以使 `maxLeftX<=minRightY`.
        就是说我们要调整搜索范围为 [low, partitionX-1].
        所以， 设 high = partitionX-1, 然后回到步骤 <2>.
```
我们注意到这里左右切分部分有可能为空。为处理这种特殊情况，我们可以相应的把`maxLeft`设为`Integer.MIN_VALUE`、
`minRight`设为`Integer.MAX_VALUE`。以下为完整的Java代码。

## 解答

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        
        // Ensure nums1 is always the shorter array
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int x = nums1.length; 
        int y = nums2.length; 
        
        // Binary Search on partitionX
        int low = 0; 
        int high = x;
        while (low <= high) {
            
            // Split the two arrays into four parts st. len(leftX) + len(leftY) = len(rightX) + len(rightY)
            int partitionX = low + (high - low) / 2;
            int partitionY = (x + y + 1) / 2 - partitionX; // +1 to keep left one more if total is odd
            
            // When left side is emtpy, set maxLeft to MIN_VALUE 
            // When right side is empty, set minRight to MAX_VALUE
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minRightX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minRightY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];
            
            // Condition satisfied: all elements in left part < right part
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                if ( (x + y) % 2 == 0) // even
                    return (Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2.0;
                else // odd: left has extra
                    return (double) Math.max(maxLeftX, maxLeftY);
            }
            
            // maxLeftX too large, Move partitionX to left
            else if (maxLeftX > minRightY) {
                high = partitionX - 1;
            }
            
            // maxLeftX too small so that maxLeftY too large, move partitionX to right
            else {
                low = partitionX + 1;
            }
        }
        
        return 0;
    }
}
```

## Complexity Analysis
- **Time Complexity:** O(log(min(x, y))). 假设`x < y`, 那么我们在`[0, x]`的范围内进行了二分搜索，每次搜索范围会减半，因此我们总共需要log(x)次循环。每次循环仅需constant time。因此总共时间为O(log(min(x, y))).
- **Space Complexity:** O(1)。我们并未使用任何额外的空间。

## 拓展

- 假设两个input array中有一个没有被`sorted`，我们能使用什么办法找到他们的中点呢？

## 总结

题目中的`sorted`提示我们使用二分搜索，但我们很难发现如何使用。我们需要对中位数的特性有很强的感知能力，而且需要大量训练锻炼出天才般的敏锐度才能想出这题的解法。
不过，我们可以举一反三，在今后将这个题目的思想应用至其他类似的题目中去。

## 原文链接

以上解答由下面视频讲解与解答综合得来，有增改。
- [Video Explanation by Tushar Roy](https://www.youtube.com/watch?v=LPFhl65R7ww&t=1013s)
- [MissMary's solution](https://leetcode.com/problems/median-of-two-sorted-arrays/discuss/2481/Share-my-O(log(min(mn)))-solution-with-explanation)
- [中文译文 by zhgy](https://zhuanlan.zhihu.com/p/70654378)
- [Leetcode Official Solution](https://leetcode.com/problems/median-of-two-sorted-arrays/solution/)