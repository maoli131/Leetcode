# [Leetcode 48. Rotate Image](https://leetcode.com/problems/rotate-image/)

## 题目

You are given an n x n 2D matrix representing an image. Rotate the image by 90 degrees (clockwise).

**Note:**
You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

**Example 1:**
```
Given input matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

rotate the input matrix in-place such that it becomes:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```
**Example 2:**
```
Given input matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 

rotate the input matrix in-place such that it becomes:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```

## 思路

类似于其他旋转类别的题目，我们需要观察旋转前后2D数组的变化来探索可能的解答。比如，我们注意到
Example 2中，Input Matrix的所有rows变成了columns，所有columns变成了rows：
```
Example 2中Matrix在交换行列之后：
[
  [5, 2, 13, 15],
  [1, 4, 3, 14],
  [9, 8, 6, 12],
  [11, 10, 7, 16]
]
```
然后，我们再翻转每一行的数组，就能得到最终旋转后的解答：
```
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```
因此，我们可以先将任意的Input Matrix进行Transpose操作，再Reverse每一行的数组，既能得到解答。

## 解答
``` java
class Solution {
    public void rotate(int[][] matrix) {
        // Transpose entire matrix
        transpose(matrix);
        
        // Reverse Each Row
        for (int i = 0; i < matrix.length; i++) {
            reverse(matrix[i]);
        }
    }
    
    /**
     * This method transpose the matrix (row to column, column to row)
     * @param: input 2-d array, matrix
     * @return: void
     */
    private static void transpose(int[][] matrix) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = i; j < matrix.length; j++) {
                int temp = matrix[j][i];
                matrix[j][i] = matrix[i][j];
                matrix[i][j] = temp;
            }
        }
    }
    
    /**
     * This method reverse an int array in place
     * @param: input int array
     */
    private static void reverse(int[] arr) {
        int start = 0;
        int end = arr.length - 1;
        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }
}
```

## Complexity Analysis
- **Time Complexity**: O(n^2). 整个Matrix有n * n个元素，我们在Transpose和Reverse的过程当中需要对每个元素都进行操作
- **Space Complexity**: O(1). 我们所有对Matrix的操作都是in-place的，不需要额外空间。

## 优化/其他解答
以上的解答是比较好直观理解的。基于以上的核心思想，我们同样可以通过观察Matrix前后变化，发现我们
可以通过交换Matrix四个边上的长方形、保留最中间的元素不变来达到旋转的效果。详见这个[解答](https://leetcode.com/problems/rotate-image/solution/)。

另外，我们还可以用类似于Rotate Array里面所使的[方案二](./189-RotateArray.md)来解决这道问题。
我们可以在访问所有元素的过程中，找到每个元素的另外三个旋转小伙伴，在一次操作中完成这四个元素的旋转。
这样的话，比起之前解法中我们Transpose和Reverse访问全部的元素两次，我们只用一个循环就能完成Matrix的旋转。

## 总结
- 变化类题目可以通过比较前后Matrix, Array, Graph的关系来探索可能的解答
- 尽可能把一个复杂的变换拆分成多个简单、易执行的小变化

## Reference
[Leetcode Official Solution](https://leetcode.com/problems/rotate-image/solution/)