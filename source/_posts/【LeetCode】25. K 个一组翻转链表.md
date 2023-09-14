title: 【LeetCode】25. K 个一组翻转链表
date: 2021-03-03 06:39:59
categories: LeetCode
tags: []
---
https://leetcode-cn.com/problems/reverse-nodes-in-k-group

> 给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。
k 是一个正整数，它的值小于或等于链表的长度。
如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

## 思路
#### 我的思路是这样的
#### step1 先找到一组长度为k的链表组，获取第n*k个结点为tail
#### step2 将这组链表反转，返回
![一组链表反转示意](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2021/03/2638907492.jpg)
#### step3 将反转后的链表接入原来tail
#### step4 递归
```java
public class ReverseKGroup {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || head.next == null) return head;
        ListNode tail = head;
        // 划分一组链表(如k=3 就是[0,3),[3,6)......)
        for (int i = 0; i < k; i++) {
            if (tail == null) return head;
            tail = tail.next;
        }

        // 接收反转后的一组链表
        ListNode resHead = reverse(head, tail);
        // 将反转后的链表接起来
        head.next = reverseKGroup(tail, k);

        return resHead;
    }

    // 原地反转链表
    public ListNode reverse(ListNode head, ListNode tail) {
        // 暂存前一个链表结点
        ListNode pre = null;
        // 暂存下一个链表结点
        ListNode next;
        while (head != tail) {
            next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
}```
