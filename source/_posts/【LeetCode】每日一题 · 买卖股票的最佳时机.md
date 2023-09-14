title: 【LeetCode】每日一题 · 买卖股票的最佳时机
date: 2020-03-09 12:35:00
categories: LeetCode
tags: []
---
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n == 0) return 0;
        int[] dp = new int[n];
        int minprice = prices[0];
        dp[0] = 0;
        for (int i = 1; i < n ; i++) {
            minprice = Math.min(minprice,prices[i]);
            dp[i] = Math.max(dp[i-1], prices[i] - minprice);
        }
        return dp[n-1];
    }
}
```