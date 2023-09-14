title: 【LeetCode】第71场 双周赛补题
date: 2022-03-04 12:51:13
categories: LeetCode
tags: []
---
# 第71场 双周赛补题

## 第一题 [2160. 拆分数位后四位数字的最小和](https://leetcode-cn.com/problems/minimum-sum-of-four-digit-number-after-splitting-digits/)

### 题目

[scode type="share"]

给你一个四位 正 整数 num 。请你使用 num 中的 数位 ，将 num 拆成两个新的整数 new1 和 new2 。new1 和 new2 中可以有 前导 0 ，且 num 中 所有 数位都必须使用。

比方说，给你 num = 2932 ，你拥有的数位包括：两个 2 ，一个 9 和一个 3 。一些可能的 [new1, new2] 数对为 [22, 93]，[23, 92]，[223, 9] 和 [2, 329] 。
请你返回可以得到的 new1 和 new2 的 最小 和。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/minimum-sum-of-four-digit-number-after-splitting-digits
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

[/scode]



### 思路

首先我们知道，两个两位数相加一般来说都是比一个一位数加上一个三位数来的小的。所以这里我们只需要把这四个数变成两个两位数即可，这样我们只需要把这两个两位数的十位为最小的两个数，然后剩下的两个数作为百位即可。

- 2932 --拆分->[2, 9, 3, 2] --排序--> [2, 2, 3 ,9] --重组-> [2, 3] + [2, 9] = 52



```java
class Solution {
    public int minimumSum(int num) {
        int[] nums = new int[4];
        int cnt = 0;
        while(num > 0) {
            int remind = num % 10;
            num /= 10;
            nums[cnt++] = remind;
        }
        Arrays.sort(nums);
        return nums[0]*10 + nums[1] * 10 + nums[2] + nums[3];
    }
}
```

## 第二题 [2161. 根据给定数字划分数组](https://leetcode-cn.com/problems/partition-array-according-to-given-pivot/)

### 题目

[scode type="share"]

给你一个下标从 0 开始的整数数组 nums 和一个整数 pivot 。请你将 nums 重新排列，使得以下条件均成立：

所有小于 pivot 的元素都出现在所有大于 pivot 的元素 之前 。
所有等于 pivot 的元素都出现在小于和大于 pivot 的元素 中间 。
小于 pivot 的元素之间和大于 pivot 的元素之间的 相对顺序 不发生改变。
更正式的，考虑每一对 pi，pj ，pi 是初始时位置 i 元素的新位置，pj 是初始时位置 j 元素的新位置。对于小于 pivot 的元素，如果 i < j 且 nums[i] < pivot 和 nums[j] < pivot 都成立，那么 pi < pj 也成立。类似的，对于大于 pivot 的元素，如果 i < j 且 nums[i] > pivot 和 nums[j] > pivot 都成立，那么 pi < pj 。
请你返回重新排列 nums 数组后的结果数组。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/partition-array-according-to-given-pivot
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

[/scode]

### 思路

一开始我想用快速排序的思路写的，然后xjb写了一通不太对，然后干脆就暴力一点开三个数组，一个存比```Pivot```小的叫做```left[]```, 比```pivot```大的叫做```right[]```, 和```Pivot```一样大的叫做```mid[]```，然后把他们合并，复杂度应该是```O(N)```

### 代码

```java
class Solution {
    public int[] pivotArray(int[] nums, int pivot) {
        int posl = 0;
        int posr = 0;
        int posm = 0;
        int n = nums.length;
        int[] l = new int[n];
        int[] r = new int[n];
        int[] m = new int[n];
        for (int num : nums) {
            if (num < pivot) {
                l[posl++] = num;
            } else if (num > pivot) {
                r[posr++] = num;
            } else {
                m[posm++] = num;
            }
        }
        int cnt = 0;
        for (int i = 0; i < posl; i++) {
            nums[cnt++] = l[i];
        }
        for (int i = 0; i < posm; i++) {
            nums[cnt++] = m[i];
        }
        for (int i = 0; i < posr; i++) {
            nums[cnt++] = r[i];
        }
        return nums;
    }
}
```

## 第三题 [2162. 设置时间的最少代价](https://leetcode-cn.com/problems/minimum-cost-to-set-cooking-time/)

### 题目 

[scode type="yellow"]

这题读题有点难受，我看了一些别人的理解才想清楚是怎么回事。

看了一下才理解，startAt就是初始数字是多少，move就是换数字，push就是确定键。 然后就能理解下面的行为。

```
- 1 0 0 0 ，表示 10 分 0 秒。
  手指一开始就在数字 1 处，输入 1 （代价为 1），移到 0 处（代价为 2），输入 0（代价为 1），输入 0（代价为 1），输入 0（代价为 1）。
  总代价为：1 + 2 + 1 + 1 + 1 = 6 。这是所有方案中的最小代价。
```

[/scode] 

### 思路

这题读懂题后我们知道如果前导为0的话我们直接不用管前面的0，比如输入 `0` `0` `0` `8` （四个数字），表示 `0` 分 `8` 秒。所以只用设定8秒就行。**当然这里我们要考虑两种情况，就是后面的秒大于60和小于60的情况，我们只需要考虑```(分钟数，秒数)``` 和 ```(分钟数-1， 秒数+60)```两种情况，并且判断分钟数和秒数是否合法即可。**我们以```startAt = 1, ```,  ```moveCost = 2```, ```pushCost = 1```, ```targetSeconds = 600```为例子，我们计算第一种情况下的数据```(minutes=10, seconds=00)```,我们的```startAt```为```1```所以我们不需要移动只需要执行```push```即可，所以```ansCost += pushCost```,然后我们发现当前为```0```,``` 0 != 1```所以移动一位有```ansCost += moveCost```, 这时候我们需要更新下当前的```startAt  = now```.

### 代码

```java
class Solution {
   public int minCostSetTime(int startAt, int moveCost, int pushCost, int targetSeconds) {
        // case 1
        int minute = targetSeconds / 60;
        int second = targetSeconds % 60;
        int ans1 = minCostTime(startAt, moveCost, pushCost, minute, second);
        int ans2 = minCostTime(startAt, moveCost, pushCost, minute-1, second+60);
        return Math.min(ans1, ans2);
    }

    public int minCostTime(int startAt, int moveCost, int pushCost, int minute, int second) {
        if (minute > 99 || minute < 0 || second > 99) {
            return Integer.MAX_VALUE;
        }
        // section1 60秒的情况
        int[] nums = new int[4];
        nums[0] = minute/10;
        nums[1] = minute%10;
        nums[2] = second/10;
        nums[3] = second%10;
        int cnt = 0;
        while (cnt  < 4 && nums[cnt] == 0) {
            cnt++;
        }
        int ansCost = 0;
        int prev = startAt;
        for (int i = cnt; i < 4; i++) {
            int d = nums[i];
            if (d != prev) {
                prev = d;
                ansCost += moveCost;
            }
            ansCost += pushCost;
        }
        return ansCost;
    }
}
```