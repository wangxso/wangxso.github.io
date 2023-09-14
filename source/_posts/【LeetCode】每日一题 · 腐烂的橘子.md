title: 【LeetCode】每日一题 · 腐烂的橘子
date: 2020-03-04 13:01:00
categories: LeetCode
tags: []
---
[LeetCode 994 链接点我](https://leetcode-cn.com/problems/rotting-oranges/)
## 题干

> 在给定的网格中，每个单元格可以有以下三个值之一：

>- 值 0 代表空单元格；
- 值 1 代表新鲜橘子；
- 值 2 代表腐烂的橘子。

>每分钟，任何与腐烂的橘子（在 4 个正方向上）相邻的新鲜橘子都会腐烂。

>返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1。

## 思路
一开始我是想暴力的(~~莽夫就是这样~~),后来感觉这题有点和搜索题目的意思但是我还是喜欢莽。最后莽不过就直接换了思路，可以看出橘子的扩散类似于波纹扩散，所以果断采用<font color="red">BFS</font>

 - 先记录新鲜橘子的数量和腐坏的橘子的位置；
 
- 在进行bfs出队列入队了操作，每次将符合要求的入队，这一页每次入队的就是没有遍历过得(因为规定了原来是1变成2的才能入队)，注意判断边界

- 注意还剩下新鲜橘子的判断

## 代码
```java
class Solution {
    //BFS
    public int orangesRotting(int[][] grid) {
        int[][] pos = {{1,0},{0,1},{-1,0},{0,-1}};
        Queue<int[]> queue = new LinkedList<>();
        int n = grid.length;
        int m = grid[0].length;
        int count = 0;//新鲜橘子的数量
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m ; j++) {
                if (grid[i][j] == 1){
                    count++; //记录新鲜橘子的数量
                }else if (grid[i][j] == 2){
                    queue.add(new int[]{i,j});
                }
            }
        }

        int minus = 0;//经过的时间
        while (count > 0 && !queue.isEmpty()){
            minus++;
            int s = queue.size();
            for (int i = 0; i < s ; i++) {
                int[] orange = queue.poll();
                int r = orange[0];
                int c = orange[1];
                if (r-1>=0 && grid[r-1][c] == 1){
                    grid[r-1][c] = 2;
                    System.out.println(grid[r-1][c]);
                    count--;
                    queue.add(new int[]{r-1,c});
                }

                if (r+1 < n && grid[r+1][c] == 1){
                    grid[r+1][c] = 2;
                    count--;
                    queue.add(new int[]{r+1,c});
                }

                if (c-1 >= 0 && grid[r][c-1] == 1){
                    grid[r][c-1] = 2;
                    count--;
                    queue.add(new int[]{r,c-1});
                }

                if (c+1 < m && grid[r][c+1] == 1){
                    grid[r][c+1] = 2;
                    count--;
                    queue.add(new int[]{r,c+1});
                }

            }
        }

        if (count > 0){
            minus = -1;
        }
        System.out.println(minus);
        return minus;
    }
}
```

