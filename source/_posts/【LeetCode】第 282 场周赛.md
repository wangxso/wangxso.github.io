title: 【LeetCode】第 282 场周赛
date: 2022-02-27 06:30:58
categories: 算法
tags: []
---
# 第 282 场周赛

十点起床，打了会原神，才记起来有周赛。做了两题就想去吃饭。堕落了啊。

## 第一题 [6008. 统计包含给定前缀的字符串](https://leetcode-cn.com/problems/counting-words-with-a-given-prefix/)

### 题意

水题，就是判断下前缀是否包含```pref```，然后统计下个数。我直接用Java的API写了，手写的话也不用几分钟。

### 代码

```java
class Solution {
    public int prefixCount(String[] words, String pref) {
        int n = 0;
        for(int i = 0;i < words.length; i++) {
            if(words[i].startsWith(pref)) {
                n++;
            }
        }
        return n;
    }
}
```

## 第二题 [6009. 使两字符串互为字母异位词的最少步骤数](https://leetcode-cn.com/problems/minimum-number-of-steps-to-make-two-strings-anagram-ii/)

### 题意

增加一些字符让两个字符串```s```和```t```变成字母异位次，也就是字母相同但是顺序不同的两个字符串。

### 思路

这里我们发现只要增加两个串里面不存在的字符或者不够个数的字符即可。我们这里统计一下第一个字符串的字符个数，然后减去第二个字符串的字符个数，如果数组出现负数那就是不足的需要补上，正数那就是第一个字符串不足的。我们把正数和负数的绝对值相加就能得到答案。

### 代码

```java
class Solution {
    public int minSteps(String s, String t) {
        int[] count = new int[26];
        int n = 0;
        for(int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
        }
        for(int i = 0; i < t.length(); i++) {
            count[t.charAt(i) - 'a']--;
        }
        for(int i = 0 ; i <= 25; i++) {
            n += Math.abs(count[i]);
        }
        return n;
    }
}
```

## 第三题 [6010. 完成旅途的最少时间](https://leetcode-cn.com/problems/minimum-time-to-complete-trips/)

### 题意

给你每辆公交车完成一趟需要的时间```time[]```，并且公交车在完成后可以继续使用。让你求完成```totalTrips```趟需要的最少时间。

### 思路

我一开始的想法是把```time```进行排序，然后每次都加一下time[0]，```ans++``` ，每次统计下时间```totalTime```然后判断time中是否存在```time[i] >= totalTime```然后将```ans++```。但是我们需要判断每个time[i]在一个周期只能加一次，而这个周期是time中最大的那辆车的一趟的时间。很麻烦，也很绕。

然后我看了别人的解法。他们是使用二分的解法，对于我来说不是很好想。

显然这题返回的时间不会超过每次都跑一趟时间最少的那辆公交车的时间也就是```time[0]*totalTrips```(这里time[0]是排序后最小的时间)。然后我们只需要```1-->(time[0]*totalTrips)```之间寻找我们的答案即可。我们统计算出当前时间下能进行的趟数，如果大于我们就往左边搜(l -> mid - 1)，小于就往右边搜(mid+1, r)

### 代码

```java
class Solution {
    public long minimumTime(int[] time, int totalTrips) {
        Arrays.sort(time);
        long lo = 1;
        long hi = (long)time[0] * totalTrips;
        while(lo <= hi) {
            long mid = (lo + hi) / 2;
            long trips = 0;
            
            for(int num:time) {
                trips += mid/num;
            }

            if(trips >=totalTrips) {
                hi = mid - 1;
            }else {
                lo = mid + 1;
            }
        }
        return lo;
    }
}
```



