title: 【LeetCode】只出现一次的数字
date: 2019-10-30 12:15:00
categories: Java
tags: []
---
## [题目链接]("https://leetcode-cn.com/problems/single-number/")
------
这是一道很水的题目(我虽然有想法但是我还是被大佬的思考折服了)
> 给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

我的想法是直接一个map里面key为数字，value为出现的次数，只需要O(n)就能找出来了。
代码如下:
```java
	public int singleNumber(int[] nums) {
        int res = -1;
        Map<Integer, Integer> map = new HashMap<>();
        for(Integer num : nums){
            if(map.get(num)!=null){
                Integer value = map.get(num);
                value+=1;
                map.put(num, value);
            }else{
                map.put(num, 1);
            }
        }

        for(Integer key:map.keySet()){
            if(map.get(key) == 1){
                res = key;
            }
        }
        return res;
    }
```

可是大佬毕竟是大佬，当我看到前面那个2ms的我被折服了(我好菜啊)
```java
    public int singleNumber(int[] nums) {
        int num = 0;
        for (int n : nums) {
            num = num^n;
        }
        return num;
    }
```
只需要异或(^)来判断一下这个值是否再次出现不就行了！！！:scream: :scream: 
