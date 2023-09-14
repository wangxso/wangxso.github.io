title: 【LeetCode HTOP100】03 无重复字符的最长子串
date: 2021-02-27 03:05:00
categories: LeetCode
tags: []
---
https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/

## 方法一 暴力法(官方解析用HashSet 我偏不用)
```java
// 暴力法 算法复杂度O(N^2)
    public int lengthOfLongestSubstring(String s) {
        // 初始化返回值
        int ans = 0;
        // 字符串为空返回0
        if (s == null || s.equals("")) return ans;
        char[] chars = s.toCharArray();
        //每次将i的作为起始位
        for (int i = 0; i < chars.length; i++) {
            int temp = 0;
            System.out.println(i);
            // 初始化Map 每次循环都要重新构造 判断构造的字符串中的字符是否重复
            Map<Character, Integer> map = new HashMap<>();
            for (int j = i; j < chars.length; j++) {
                // 判断构造的字符串中的字符是否重复
                if (!map.containsKey(chars[j])){
                    temp += 1;
                } else {
                    break;
                }
                map.put(chars[j], j);
            }
            // 取最大值
            ans = Math.max(ans, temp);
        }
        return ans;
    }
	```
## 方法二 滑动窗口
```java
// 滑动窗口
    public int lengofLonger(String s){
        // 初始化返回值
        int ans = 0;
        // 字符串为空返回0
        if (s == null || s.equals("")) return ans;
        // 当前窗口的最左端
        int left = 0;
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            if (map.containsKey(s.charAt(i))) {
                // 构造的字符串包含字符就重新搞最左边
                left = Math.max(left, map.get(s.charAt(i)) + 1);
            }
            map.put(s.charAt(i), i);
            ans = Math.max(ans, i-left+1);
        }
        return ans;
    }
	```