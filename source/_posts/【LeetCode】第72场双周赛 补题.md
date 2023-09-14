title: 【LeetCode】第72场双周赛 补题
date: 2022-02-20 07:49:00
categories: LeetCode
tags: []
---
# 第72场双周赛 蔚来

## 第一题 5996. 统计数组中相等且可以被整除的数对

### 题意 & 思路

简单的就是找出满足这些条件

- ```0 <= i < j < n```
- ```nums[i] == nums[j]```
- ```(i * j)%k==0```

的这样一个i，j对的个数。而且该题的数据范围很小所以我们直接双重循环解决。

> - `1 <= nums.length <= 100`
> - `1 <= nums[i], k <= 100`

### 代码

```java
class Solution {
    public int countPairs(int[] nums, int k) {
        int ans = 0;
        int n = nums.length;
        for(int i = 0; i < n; i++) {
            for(int j = i+1; j < n; j++) {
                if((i*j) % k == 0 && (nums[i] == nums[j])) {
                    ans++;
                }
            }
        }
        return ans;
    }
}
```

算法时间复杂度O(N^2)

## 第二题 5997. 找到和为给定整数的三个连续整数

### 题意

给你一个整数你把它拆分为三个连续整数的和。如果不能拆分就返回空。

### 思路

我们看下33之类的三个数相加必然包含了33整除3。

我们夹着要求的数字为```x```,则有```(x/3) + (x/3) - 1 + (x/3) +1 <= x```并且有```x >= (x/3) + (x/3) + 1 + (x/3) + 2)```

我们只需要求这两个就行了。

### 代码

```java
class Solution {
    public long[] sumOfThree(long num) {
        long tmp = num / 3;
        long minN = (tmp-1) + tmp + tmp+1;
        long maxN = tmp + tmp+1 + tmp+2;
        if(minN == num) {
            return new long[]{tmp-1, tmp, tmp+1};
        } else if(minN == maxN) {
            return new long[]{tmp, tmp+1, tmp+2};
        } else {
            return new long[]{};
        }
    }
}
```

算法时间复杂度O(1)

## 第三题 5998. 拆分成最多数目的偶整数之和

### 题意

给你一个整数，让你求这个整数能够拆分成多少个偶数，并且这些偶数不能相等。

### 思路

显然奇数不能被拆分。

我们把这个整数减去一些偶数这些偶数从小到大分别是2,4,6,8,10, ........,这样的，但是并不是所有的都能按顺序拆分我们要判断下他能不能拆分。

比如28，

- 28 - 2 = 26
- 26 - 4 = 22
- 22 - 6 = 16

这里我们判断下16的一半能否被后面一个完整的拆分，也就是判断下```(16 - 8) > 8 ?```，我们得到的答案是false，那么我们就不要把16拆分直接使用16即可。

那么我们的答案就是```[2,4,6,16]```

### 代码

```java
class Solution {
    public List<Long> maximumEvenSplit(long finalSum) {
        List<Long> ansList = new ArrayList<>();
        if(finalSum % 2 != 0) {
            return ansList;
        } 
        long tmp = finalSum;
        long cnt = 2;
        while(tmp >= cnt) {
            if(tmp - cnt > cnt) {
                ansList.add(cnt);
                tmp -= cnt;
                cnt+=2;
            } else {
                ansList.add(tmp);
                break;
            }
        }
        return ansList;
    }
}
```

算法时间复杂度O(√n)

## 第四题 5999. 统计数组中好三元组数目

还不会做。。。。