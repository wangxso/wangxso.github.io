title: 【剑指 Offer】 22. 链表中倒数第k个节点
date: 2021-04-06 10:04:00
categories: LeetCode
tags: []
---
> 输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。
>
> 例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。
>
>  
>
> 示例：
>
> 给定一个链表: 1->2->3->4->5, 和 k = 2.
>
> 返回链表 4->5.
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解法（保持步进为K）

首先让一个快指针先走K步，然后一个慢指针和快指针一起走，两者保持K的距离，则当快指针走到尽头，慢指针就是倒数第K个元素。

代码如下：

```java
public ListNode getKthFromEnd(ListNode head, int k) {
        if (head==null || head.next == null) return head;
        ListNode fast = head;
        ListNode slow = head;
        for (int i = 0; i < k; i++) {
            fast = fast.next;
        }
        while (fast!=null) {
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
```

