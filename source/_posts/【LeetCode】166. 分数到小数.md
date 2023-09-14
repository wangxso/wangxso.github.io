title: 【LeetCode】166. 分数到小数
date: 2020-04-08 03:41:00
categories: LeetCode
tags: []
---
# 166. 分数到小数
> 给定两个整数，分别表示分数的分子 numerator 和分母 denominator，以字符串形式返回小数。

> 如果小数部分为循环小数，则将循环的部分括在括号内。

> 示例 1:

> 输入: numerator = 1, denominator = 2
输出: "0.5"
示例 2:

> 输入: numerator = 2, denominator = 1
输出: "2"
示例 3:

> 输入: numerator = 2, denominator = 3
输出: "0.(6)"

> 来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/fraction-to-recurring-decimal
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 思路：
刚开始我看到这题非常的乱，做了将近40分钟都没做出来，我只想着把他变成小数然后toString，其实想想也是可以的emm，但是还是比较麻烦要判断的条件比较多。后面看了大佬的题解瞬间我升华了（蒟蒻本蒻就是我了）

#### 敲黑板！！！
首先我们要认知到所有整数相除除不尽的都是循环小数。
然后我们只要分三种情况就行：
1. 除的尽的，无小数部分
2. 除的尽的，有小数部分
3. 除不尽的

判断重复部分我们可以用HashMap来实现。
然后我们要使用模拟竖式运算来求

$$ \begin{array}{rll}↵ 0.16 \\ 6 {\overline{\smash{\big)}\,1.00}} \\[-1pt] \underline{0\phantom{.00}} \\[-1pt] 1\phantom{.}0 \phantom{0} && ...\ 余数为\ 1，标记\ 1\ 出现在位置\ 0。\\[-1pt] \underline{\phantom{0}6\phantom{0}} \\[-1pt] \phantom{0}40 && ...\ 余数为\ 4，标记\ 4\ 出现在位置\ 1。\\[-1pt]↵\underline{\phantom{0}36} \\[-1pt] \phantom{00}4 && ...\ 余数为\ 4，在位置\ 1\ 出现过，所以循环节从位置\ 1\ 开始，为\ 1(6)。\\[-1pt] \end{array} $$

## 代码
```java
public String fractionToDecimal(int numerator, int denominator) {
        long num = numerator;
        long den = denominator;
        String sign  = "";
        //确定符号
        if (num > 0 && den < 0 || num < 0 && den > 0 ) {
            sign  = "-";
        }
        //转为正数
        num = Math.abs(num);
        den = Math.abs(den);
        //记录整数部分
        long integer = num / den;
        //计算余数
        num = num - integer * den;
        HashMap<Long, Integer> hashMap = new HashMap<>();
        int index = 0;
        String decimal = ""; //记录小数部分
        int repeatIndex = -1;//保存重复的位置
        while (num!=0){
             num *= 10;
             if (hashMap.containsKey(num)) {
                 repeatIndex = hashMap.get(num);
                 break;
             }
             hashMap.put(num, index);
             long decimalPlace = num / den;
             decimal = decimal + decimalPlace;
             num = num - decimalPlace * den;
             index++;
        }
        if (repeatIndex != -1) {
            return sign + integer + "." + decimal.substring(0, repeatIndex) + "(" + decimal.substring(repeatIndex)+")";
        } else {
            if (decimal.equals("")) {
                return sign + integer;
            } else {
                return sign + integer + "." +decimal;
            }
        }
    }
```
