title: 【剑指Offer】04. 二维数组中的查找
date: 2021-02-28 08:55:49
categories: LeetCode
tags: []
---
## 思路一 暴力法
#### 暴力的遍历整个二位数组找到是否存在这个数，算法复杂度O(N^2)，代码如下:
```java

class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
      for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                if (matrix[i][j] == target) return true;
            }
        }
        return false;
    }
}
```

## 思路二 二叉排序树
#### 仔细观察我们会发现从右上角开始，左右衍生，就像一棵二叉排序树。
> 样例数据的二叉排序树
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2021/02/3267795224.png)

#### 小于当前的就往左(col--)，大于当前的就往右(row++)

代码如下
```java
class Solution {
// 类似于排序二叉树
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix == null || matrix.length == 0) {
            return false;
        }
        int n = matrix.length;
        int m = matrix[0].length;
        int row = 0;
        int col = m - 1;
        while (col >= 0 && row < n) {
            int temp = matrix[row][col];
            if (target == temp) return true;
            else if (target > temp) row++; // 大于它就往右边走(大的)
            else if (target < temp) col--;
        }
        return false;
    }
}
```