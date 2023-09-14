title: 【LeetCode HTOP100】 02  两数相加
date: 2021-02-26 12:32:00
categories: LeetCode
tags: []
---
https://leetcode-cn.com/problems/add-two-numbers/

## 思路一
#### 将ListNode转换为数字然后将两个数字相加，在转换为ListNode，算法复杂度O(4N)
```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode temp = null;
        int count = 0;
        int sum1 = 0;
        int sum2 = 0;
        List<Integer> num1 = new ArrayList<>();
        List<Integer> num2 = new ArrayList<>();
        while (l1!=null){
            temp = l1;
            l1 = l1.next;
            sum1 += Math.pow(10,count) * temp.val;
            num1.add(temp.val);
            count++;
        }
        count = 0;
        while (l2!=null){
            temp = l2;
            l2 = l2.next;
            sum2 += Math.pow(10,count) * temp.val;
            num2.add(temp.val);
            count++;
        }
        int n = Math.max(num1.size(), num2.size());
        int[] res = new int[100000];
        for (int i=0;i<100000;i++){
            res[i] = 0;
        }
        for (int i = 0; i < n ; i++) {
            if (num1.size() <= i){
               res[i] += num2.get(i);
            }else if(num2.size() <= i){
                res[i] += num1.get(i);
            }else {
                res[i] += num1.get(i) + num2.get(i);
            }
            if (res[i] >= 10){
                res[i+1] += res[i] / 10;
//                System.out.println("res[i]"+res[i]+" ,"+res[i+1]);
                res[i] = res[i] % 10;
            }
        }
        ListNode listNode = null;
        for (int i=0;i < n+1;i++){
            if (i==0){
                listNode = new ListNode(res[i]);
                temp = listNode;
            }else {
                if (i == n && res[i] <= 0){
                    continue;
                }
                listNode.next = new ListNode(res[i]);
                listNode = listNode.next;
            }
        }
        return temp;
    }
}

```

## 思路二
#### 每次相加，判断是否进位，进位就将进位的值赋值到后一个LinkedNode里面，然后每次都这么算。算法复杂度大概是O（N）
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode res = new ListNode();
        // 返回的时候的，记录初始位置，如果返回res的话不会在初始位置
        ListNode ans = res;
        // 随便判断一下其实写成true也行
        while (l1!=null){
            // 相加l1的值加上l2的值然后加上相加的进位
            int temp = l1.val + l2.val + res.val;
            // 判断是否相加后还是大于10
            if (temp  < 10) {
                res.val = temp;
                // 两个都到尾部就结束
                if (l1.next == null && l2.next == null) return ans;
                // 创建新的下一个
                res.next = new ListNode();
                res = res.next;
            } else {
                // pre是十位 suf是个位
                int pre = temp / 10;
                int suf = temp % 10;
                // 当前为个位的值
                res.val = suf;
                res.next = new ListNode();
                res = res.next;
                // 下一个为十位的值确保能够作为进位数
                res.val = pre;
            }

            // 两者如果不等长就新建新的结点确保不让长的链表加到null的值
            if (l1.next == null) {
                l1.next = new ListNode(0);
            }
            if (l2.next == null) {
                l2.next = new ListNode(0);
            }
            // 往下找
            l1 = l1.next;
            l2 = l2.next;
        }
        return ans;
    }
}
```