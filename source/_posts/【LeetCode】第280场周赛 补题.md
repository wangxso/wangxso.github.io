title: 【LeetCode】第280场周赛 补题
date: 2022-02-18 09:41:27
categories: LeetCode
tags: []
---
# 第280场周赛 补题



## 第一题 2169. 得到 0 的操作数

水题一看就知道模拟一下就可以了，可以说是签到题了。

### 题目描述

> 给你两个 **非负** 整数 `num1` 和 `num2` 。
>
> 每一步 **操作** 中，如果 `num1 >= num2` ，你必须用 `num1` 减 `num2` ；否则，你必须用 `num2` 减 `num1` 。
>
> - 例如，`num1 = 5` 且 `num2 = 4` ，应该用 `num1` 减 `num2` ，因此，得到 `num1 = 1` 和 `num2 = 4` 。然而，如果 `num1 = 4`且 `num2 = 5` ，一步操作后，得到 `num1 = 4` 和 `num2 = 1` 。
>
> 返回使 `num1 = 0` 或 `num2 = 0` 的 **操作数** 。
>
> **示例 1：**
>
> ```
> 输入：num1 = 2, num2 = 3
> 输出：3
> 解释：
> - 操作 1 ：num1 = 2 ，num2 = 3 。由于 num1 < num2 ，num2 减 num1 得到 num1 = 2 ，num2 = 3 - 2 = 1 。
> - 操作 2 ：num1 = 2 ，num2 = 1 。由于 num1 > num2 ，num1 减 num2 。
> - 操作 3 ：num1 = 1 ，num2 = 1 。由于 num1 == num2 ，num1 减 num2 。
> 此时 num1 = 0 ，num2 = 1 。由于 num1 == 0 ，不需要再执行任何操作。
> 所以总操作数是 3 。
> ```
>
> **示例 2：**
>
> ```
> 输入：num1 = 10, num2 = 10
> 输出：1
> 解释：
> - 操作 1 ：num1 = 10 ，num2 = 10 。由于 num1 == num2 ，num1 减 num2 得到 num1 = 10 - 10 = 0 。
> 此时 num1 = 0 ，num2 = 10 。由于 num1 == 0 ，不需要再执行任何操作。
> 所以总操作数是 1 。
> ```
>
> 

### 题意

让你操作当```num1```大于```num2```的时候```num1```变成```num1-num2```，当```num1 < num2```的时候让```num2```变成```num2 - num1```，直到```num1```或```num2```之中有一个为0。

### 代码

```java
class Solution {
    public int countOperations(int num1, int num2) {
        int cnt = 0;
        while(num1 != 0 && num2 !=0) {
            cnt++;
            if(num1 > num2) {
                num1 = num1 - num2;
            } else {
                num2 = num2 - num1;
            }
        }
        return cnt;
    }
}
```

## 第二题 2170. 使数组变成交替数组的最少操作数

这题我一看就头大感觉自己最烦这种操作xx把什么变成xx的问题了。然后我一开始想的是查找不满足这些条件的数字有几个并且进行去重但是样例能过，但是这样就不是很严谨了。

下面是看了别人的答案后才想出来的答案。

### 题目描述

> 给你一个下标从 **0** 开始的数组 `nums` ，该数组由 `n` 个正整数组成。
>
> 如果满足下述条件，则数组 `nums` 是一个 **交替数组** ：
>
> - `nums[i - 2] == nums[i]` ，其中 `2 <= i <= n - 1` 。
> - `nums[i - 1] != nums[i]` ，其中 `1 <= i <= n - 1` 。
>
> 在一步 **操作** 中，你可以选择下标 `i` 并将 `nums[i]` **更改** 为 **任一** 正整数。
>
> 返回使数组变成交替数组的 **最少操作数** 。
>
> 
>
> **示例 1：**
>
> ```
> 输入：nums = [3,1,3,2,4,3]
> 输出：3
> 解释：
> 使数组变成交替数组的方法之一是将该数组转换为 [3,1,3,1,3,1] 。
> 在这种情况下，操作数为 3 。
> 可以证明，操作数少于 3 的情况下，无法使数组变成交替数组。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [1,2,2,2,2]
> 输出：2
> 解释：
> 使数组变成交替数组的方法之一是将该数组转换为 [1,2,1,2,1].
> 在这种情况下，操作数为 2 。
> 注意，数组不能转换成 [2,2,2,2,2] 。因为在这种情况下，nums[0] == nums[1]，不满足交替数组的条件
> ```

### 题意

题目的要求是让数组的元素交替相等，相邻的元素不相等。

我们可以看出如果要使得这样的数组成立， 我们就需要把奇数位的数字相同，偶数位的数字相同，并且奇数位和偶数位的数字不想同。那么我们利用贪心的思想可以知道我们只需要选择奇数位出现次数最多的数字和偶数位出现次数最多的次数。**但是存在特殊的情况那就是奇数位出现最多的数字和偶数位出现最多的数字相等，那么我们就选择Max(奇数位最多+偶数位次多，偶数位最多+奇数位次多)**然后把数字的总个数减去它。



### 代码

```java
import java.util.*;
class Solution {
     public int minimumOperations(int[] nums) {
        // 贪心
        if (nums.length == 1) return 0;
        int n = nums.length;
        Map<Integer, Integer> oddMap = new HashMap<>();
        Map<Integer, Integer> evenMap = new HashMap<>();
        // 统计数字在奇数位和偶数位出现的次数
        for (int i = 0; i < n; i++) {
            if (i % 2 != 0) {
                oddMap.put(nums[i], oddMap.getOrDefault(nums[i], 0) + 1);
            } else {
                evenMap.put(nums[i], evenMap.getOrDefault(nums[i], 0) + 1);
            }
        }
        // 保存出现次数最多的数字(xxxKey).和他出现的次数，还有奇数位/偶数位出现第二多的数字的次数。
        int oddKey = 0, odd1st = 0, odd2nd = 0;
        int evenKey = 0, even1st = 0, even2nd = 0;

        for (Integer key:oddMap.keySet()) {
            int val = oddMap.get(key);
            if (val > odd1st) {
                odd2nd = odd1st;
                odd1st = val;
                oddKey = key;
            } else if (val > odd2nd){
                odd2nd = val;
            }
        }

        for (Integer key:evenMap.keySet()) {
            int val = evenMap.get(key);
            if (val > even1st) {
                even2nd = even1st;
                even1st = val;
                evenKey = key;
            } else if (val > even2nd) {
                even2nd = val;
            }
        }
        // 如果这两个key不一样，那么就返回
        if (oddKey != evenKey) {
            return n - odd1st - even1st;
        } else {
            return n - Math.max(odd1st + even2nd, odd2nd + even1st);
        }
    }
}
```

## 第三题 2171. 拿出最少数目的魔法豆

观察下题目我们可以发现这是考我们思维逻辑的题目，如果想要相等我们只能吧袋子里的豆子变成袋子里已经存在的豆子的数量的某一个。然后我就来了一发暴力果然TLE了。

```java
class Solution {
    public long minimumRemoval(int[] beans) {
        int ans = Integer.MAX_VALUE;
        int n = beans.length;
        for(int i = 0; i < n; i++) {
            int tmp = 0;
            for(int j = 0; j < n; j++) {
                if(beans[i] > beans[j]) {
                    tmp += beans[j];
                } else {
                    tmp += beans[j] - beans[i];
                }
            }
            ans = Math.min(tmp, ans);
        }
        return ans;
    }
}
```

算法复杂度O(N^2)

### 题目描述

> 给你一个 **正** 整数数组 `beans` ，其中每个整数表示一个袋子里装的魔法豆的数目。
>
> 请你从每个袋子中 **拿出** 一些豆子（也可以 **不拿出**），使得剩下的 **非空** 袋子中（即 **至少** 还有 **一颗** 魔法豆的袋子）魔法豆的数目 **相等** 。一旦魔法豆从袋子中取出，你不能将它放到任何其他的袋子中。
>
> 请你返回你需要拿出魔法豆的 **最少数目**。
>
>  
>
> **示例 1：**
>
> ```
> 输入：beans = [4,1,6,5]
> 输出：4
> 解释：
> - 我们从有 1 个魔法豆的袋子中拿出 1 颗魔法豆。
>   剩下袋子中魔法豆的数目为：[4,0,6,5]
> - 然后我们从有 6 个魔法豆的袋子中拿出 2 个魔法豆。
>   剩下袋子中魔法豆的数目为：[4,0,4,5]
> - 然后我们从有 5 个魔法豆的袋子中拿出 1 个魔法豆。
>   剩下袋子中魔法豆的数目为：[4,0,4,4]
> 总共拿出了 1 + 2 + 1 = 4 个魔法豆，剩下非空袋子中魔法豆的数目相等。
> 没有比取出 4 个魔法豆更少的方案。
> ```
>
> **示例 2：**
>
> ```
> 输入：beans = [2,10,3,2]
> 输出：7
> 解释：
> - 我们从有 2 个魔法豆的其中一个袋子中拿出 2 个魔法豆。
>   剩下袋子中魔法豆的数目为：[0,10,3,2]
> - 然后我们从另一个有 2 个魔法豆的袋子中拿出 2 个魔法豆。
>   剩下袋子中魔法豆的数目为：[0,10,3,0]
> - 然后我们从有 3 个魔法豆的袋子中拿出 3 个魔法豆。
>   剩下袋子中魔法豆的数目为：[0,10,0,0]
> 总共拿出了 2 + 2 + 3 = 7 个魔法豆，剩下非空袋子中魔法豆的数目相等。
> 没有比取出 7 个魔法豆更少的方案。
> ```
>
>  
>
> **提示：**
>
> - `1 <= beans.length <= 105`
> - `1 <= beans[i] <= 105`

### 题意

给你一个bean数组，然后把这个数组中除了0之外的数字都相等，问你需要每个下标需要减去多少数字最小的总和。

### 代码

```java
import java.util.*;
class Solution {
      public long minimumRemoval(int[] beans) {
        int n = beans.length;
        long[] suff = new long[n+1];
        long[] right = new long[n+1];
        long ans = Long.MAX_VALUE;
        Arrays.sort(beans);
        // 求前缀和
        for(int i = 1; i <= n; i++) {
            suff[i] = suff[i-1] + beans[i-1];
        }
        // 求后面n个的和(后缀和)
        right[n-1] = 0;
        for (int i = n-2; i >= 0; i--) {
            right[i] = right[i+1] + beans[i+1];
        }
        for(int i = 0;i < n; i++) {
            //(n-i-1)表示i后面的每个减去beans[i]
            long tmp = suff[i] + right[i] -(n-i-1)*(long)beans[i];
            ans = Math.min(ans, tmp);
        }
        return ans;
    }
}
```

