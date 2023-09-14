title: 【LeetCode】每日一题 · 和为S的正数序列
date: 2020-03-06 03:36:00
categories: LeetCode
tags: []
---
[原题出处](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/ "原题出处")

## 题干
>输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。

>序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

## 分析
### 一、有点暴力法(O(n^2) 超时)
1.开始想到他是个等差数列当相邻的时候已知公差和前n项和求首项和有几项。(~~绝对是昨天那道题影响到了我~~)
2.Sn = na1 + (n-1)*n/2 * d
d = 1 Sn 已知
na1 + n*(n-1)/2  = Sn

#### 代码
```java
//有点暴力 O(n^2) 超时了
    public int[][] findContinuousSequence(int target) {
        int first = 0;
        int n = 0;
        int count = 0;
        ArrayList<int[]> ret = new ArrayList<>();
        for (int i = 1; i < target; i++) {
            for (int j = 0; j <target; j++) {
                first = i;
                n = j;
                int[] ans = new int[n];
                int res = n * first + n*(n-1)/2;
                if (res == target){
                    for (int k = 0; k < n ; k++) {
                        ans[k] = first + k;
                    }
                    ret.add(ans);
                    count++;
                }
            }
        }
        return ret.toArray(new int[0][]);
    }
```
### 二、看了题解后才发现的滑动窗口(双指针)法
![Notepad_202003061124_30036.png](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2020/03/805881467.png)

解析如图所示

#### 代码
```java
//滑动窗口
    public int[][] findContinuousSequence(int target) {
        int l = 1;//左边界
        int r = 1;//右边界
        int sum = 0;//窗口的数字的和
        List<int[]> ans = new ArrayList<>();
        while (l <= target / 2){
            if (sum < target){
                //右边界向右移动
                sum += r;
                r++;
            }else if (sum > target){
                //左边界向右移动
                sum-=l;
                l++;
            }else if (sum == target){
                int[] res = new int[r - l];
                for (int i = l; i < r ; i++) {
                    res[i - l] = i;
                }
                ans.add(res);
                //左边界向右移动
                sum -= l;
                l++;
            }
        }
        return ans.toArray(new int[0][]);
    }
```