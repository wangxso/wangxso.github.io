title: 【LeetCode】每日一题 · 队列的最大值
date: 2020-03-07 05:43:00
categories: LeetCode
tags: []
---
来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
##题干
请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back和 pop_front 的时间复杂度都是O(1)。

若队列为空，pop_front 和 max_value 需要返回 -1


##解析

  首先我的做法是搞两个队列一个用来存储正常加入的数，一个是优先队列用来存储最大值。但是我这样就做不到O(1)的时间复杂度了。首先优先队列调整的时间复杂度是O(logn),而且每次出队都要去除优先队列里面的值也是O(n)的时间复杂度。
  
### 代码
```java
class MaxQueue {
    Queue<Integer> queue = new LinkedList<>();
    Queue<Integer> maxQueue = new PriorityQueue<>(new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return o2 - o1;
        }
    });
    public MaxQueue() {

    }

    public int max_value() {
        if (maxQueue.size() == 0){
            return  -1;
        }else{
            int max_val = maxQueue.poll();
            maxQueue.add(max_val);
            return max_val;
        }
    }

    public void push_back(int value) {
        queue.add(value);
        maxQueue.add(value);
    }

    public int pop_front() {
        if (queue.size() == 0){
            return -1;
        }else{
            int ret = queue.poll();
            maxQueue.remove(ret);
            return ret;
        }
    }
}
```

## 别人的方法(~~我怎么想不到~~)
  利用链表的记录地址的值我们可以设置一个节点记录最大值的地址，当最大值要出去时候就移除这个节点，但是唔觉得

  他这个每次出去都要扫描一次算不算是O(n)的时间复杂度。懂得可以评论里和我说说，Thanks♪(･ω･)ﾉ
  
  
  
```java
class MaxQueue {
    //最大的节点
    private Node max;
    //根节点
    private Node root;
    //尾结点
    private Node tail;
    class Node{
        int val;
        Node next=null;
        public Node(int val){this.val=val;}
    }

    public MaxQueue() {
        this.root=new Node(-1);
        root.next=null;
        this.max=root;
        this.tail=root;
    }

    public int max_value() {
        //队列为空
        if(root==tail)return -1;
        return max.val;

    }

    public void push_back(int value) {
        //生成临时节点
        Node tmp=new Node(value);
        //链接进原来的队列
        tail.next=tmp;
        //将尾结点后移
        tail=tmp;
        //存储最大节点的位置和值
        if(max==root||max.val<=value)max=tmp;
    }

    public int pop_front() {
        if(tail==root)return -1;
        //root为哨兵
        root=root.next;//root并不代表实际节点
        if(root==max){//最大值要出去了
            Node head=root.next;//head-tail才代表实际的队列
            max=head;
            while(head!=null){
                max=max.val<=head.val?head:max;
                head=head.next;
            }
        }
        max=(max==null)?root:max;

        return root.val;
    }
}
```