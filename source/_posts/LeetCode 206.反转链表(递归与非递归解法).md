title: LeetCode 206.反转链表(递归与非递归解法)
date: 2021-11-09 10:35:40
categories: LeetCode
tags: []
---




[206. 反转链表 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/reverse-linked-list/)

### 1. 非递归(迭代)实现

这题的非递归实现比递归实现更好理解，先上代码：

```java
class Solution {
    public ListNode reverseList(ListNode head) {
       ListNode pre = null;
       ListNode cur = head;
       ListNode next = null;
       while(cur != null) {
           next = cur.next;
           cur.next = pre;
           pre = cur;
           cur = next;
       }
       return pre;
    }
}
```

我们利用当前的和前面的还有下一个的三个指针来实现链表的交换从而实现反转，具体步骤如下：

如下图，一开始我们的pre指向null，cur指向链表的第一个结点，next指向cur的下一个结点。

![初始状态](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211109165231356.png)

然后我们将1->2的这条删了，将1的next指向pre(null)，如下图所示。

![改变next指向](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211109165440594.png)

然后移动pre，cur和next指针将pre 变成当前的cur（值为1的结点），然后将cur变成他的下一个结点也就是值为2的结点，而next则是值为2的结点的下一个结点也就是值为3的结点，如下图所示。

![指针移动](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211109165610841.png)

如此往复，就可以将所有的结点反转，然后最后的头结点就是pre所在的结点。

该算法的时间复杂度为**O(N)**

### 2. 递归实现

首先我们需要将这些结点递归到最后一个结点（类似于压到栈里面），然后对于后面的结点进行反转。类似于下图。

![递归后反转示意图](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211109183240811.png)

类似于将head的next的next的指针指向head从而达到反转，head的next为null为了最后能够结束而不是构造成一个环。

代码如下：

```java
class Solution {
    public ListNode reverseList(ListNode head) {
       if(head == null || head.next == null) {
           return head;
       }
       ListNode newHead = reverseList(head.next);
       head.next.next = head;
       head.next = null;
       return newHead;
    }
}
```

算法复杂度**O(N)**

