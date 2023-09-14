title: LeetCode周赛 267 
date: 2021-11-14 04:02:07
categories: LeetCode
tags: []
---
#### 5926. 买票需要的时间

> 有 `n` 个人前来排队买票，其中第 `0` 人站在队伍 **最前方** ，第 `(n - 1)` 人站在队伍 **最后方** 。
>
> 给你一个下标从 **0** 开始的整数数组 `tickets` ，数组长度为 `n` ，其中第 `i` 人想要购买的票数为 `tickets[i]` 。
>
> 每个人买票都需要用掉 **恰好 1 秒** 。一个人 **一次只能买一张票** ，如果需要购买更多票，他必须走到 **队尾** 重新排队（**瞬间** 发生，不计时间）。如果一个人没有剩下需要买的票，那他将会 **离开** 队伍。
>
> 返回位于位置 `k`（下标从 **0** 开始）的人完成买票需要的时间（以秒为单位）。

##### 思路

利用队列模拟这个过程

##### 代码

```java
class Solution {
    class Node {
        int pos;
        int val;
    }
    public int timeRequiredToBuy(int[] tickets, int k) {
        int res = 0;
        Queue<Node> queue = new LinkedList<>();
        for (int i = 0; i < tickets.length; i++) {
            Node node = new Node();
            node.pos = i;
            node.val = tickets[i];
            queue.add(node);
        }
        while (true) {
            Node node = queue.poll();
            node.val--;
            if (node.pos == k && node.val == 0) {
                res++;
                break;
            } else if (node.val > 0) {
                queue.add(node);
            }
            res++;
        }
        return res;
    }
}
```

#### 5927. 反转偶数长度组的节点

> 给你一个链表的头节点 `head` 。
>
> 链表中的节点 **按顺序** 划分成若干 **非空** 组，这些非空组的长度构成一个自然数序列（`1, 2, 3, 4, ...`）。一个组的 **长度** 就是组中分配到的节点数目。换句话说：
>
> - 节点 `1` 分配给第一组
> - 节点 `2` 和 `3` 分配给第二组
> - 节点 `4`、`5` 和 `6` 分配给第三组，以此类推
>
> 注意，最后一组的长度可能小于或者等于 `1 + 倒数第二组的长度` 。
>
> **反转** 每个 **偶数** 长度组中的节点，并返回修改后链表的头节点 `head` 。

##### 思路

先统计链表长度，在分组，在利用[92. 反转链表 II - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

中的区间反转链表实现对于偶数分组的反转。

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
 class Group {
        int start;
        int end;
        public Group(int start, int end) {
            this.start = start;
            this.end = end;
        }

        public Group() {}
    }


    public ListNode reverseEvenLengthGroups(ListNode head) {
        if (head == null || head.next == null || head.next.next == null) {
            return head;
        }
        // 统计长度
        int length = 0;
        ListNode p = head;
        while (p!=null) {
            p = p.next;
            length++;
        }
        // 分组
        List<Group> groupList = new ArrayList<>();
        int cnt = 0;
        int lastStart = 0;
        int len = length;
        while (length >= 0) {
            length -= cnt+1;
            if (length < 0) {
                groupList.add(new Group(lastStart , len-1));
                break;
            }
            groupList.add(new Group(lastStart ,lastStart+cnt));
            cnt++;
            lastStart += cnt;
        }
        // 反转偶数组
        for (int i = 0; i < groupList.size(); i++) {
            Group g = groupList.get(i);
            if ((g.end - g.start + 1) % 2 == 0) {
                    head = reverseBetween(head,g.start+1, g.end+1);
                }
        }
        return head;
    }

    public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode pre = dummy;
        for(int i = 1; i < m; i++){
            pre = pre.next;
        }
        head = pre.next;
        for(int i = m; i < n; i++){
            ListNode nex = head.next;
            head.next = nex.next;
            nex.next = pre.next;
            pre.next = nex;
        }
        return dummy.next;
    }
}
```

#### 5928. 解码斜向换位密码

#### 5929. 处理含限制条件的好友请求



