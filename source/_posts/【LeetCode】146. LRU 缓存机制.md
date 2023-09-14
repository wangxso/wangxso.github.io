title: 【LeetCode】146. LRU 缓存机制
date: 2021-03-04 08:12:00
categories: LeetCode
tags: []
---
## LRU很经典的操作系统的算法
#### LRU就是最近最久未使用算法，首先我们要知道他是怎么运行的然后我们才能写出代码。
> LRU就是一个限定页数的内存中，这里比如是4页，有一个内存访问的序列比如4 3 3 2 1 5, 如果不在内存中就需要从硬盘中调入内存,如图所示
![Screenshot_20210304_160719_com.fluidtouch.noteshe.jpg](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2021/03/1094654036.jpg)


#### 思路一
> 我刚开始尝试使用队列来存储数据，果不其然的时间超限了。可以看看我的失败代码，不想看的可以跳过。O(∩_∩)O

```java
class LRUCache {
    private int capacity;
    Queue<Integer> keyQueue = new ArrayDeque<>();
    Map<Integer, Integer> dataMap = new HashMap<>();

    public LRUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        if (capacity <= 0 || !dataMap.containsKey(key)) return -1;
        Integer integer = dataMap.get(key);
        Queue<Integer> tempQueue = new ArrayDeque<>();
        while (!keyQueue.isEmpty()) {
            int n = keyQueue.poll();
            if (n == key){
                continue;
            }
            tempQueue.add(n);
        }
        tempQueue.add(key);
        this.keyQueue = tempQueue;
        return integer;
    }

    public void put(int key, int value) {
        if (!dataMap.containsKey(key)) {
            if (dataMap.size() >= capacity) {
                Integer poll = keyQueue.poll();
                dataMap.remove(poll);
            }
            keyQueue.add(key);
        } else {
            Queue<Integer> tempQueue = new ArrayDeque<>();
            while (!keyQueue.isEmpty()) {
                int n = keyQueue.poll();
                if (n == key){
                    continue;
                }
                tempQueue.add(n);
            }
            tempQueue.add(key);
            this.keyQueue = tempQueue;
        }
        dataMap.put(key, value);
    }
}```


#### 思路二
> 这里我使用来了双向队列来解决每次移动都要O(N)的算法复杂度使得每次移动其实只要O（1）来解决。只需要删除原来链表的结点，并把他加到头部。

```java
public class LRUCache {
    // 双向链表
    class DLinkNode {
        public DLinkNode pre;
        public DLinkNode next;
        public int key;
        public int val;
        public DLinkNode(int key, int val) {
            this.key = key;
            this.val = val;
        }
        public DLinkNode(){}
    }
    // key和node做映射
    private Map<Integer, DLinkNode> cache = new HashMap<>();
    // 当前链表中的结点数
    private int size;
    // 最大的结点数
    private int capacity;
    // 初始化的时候建好head和tail可以减少算法编写的复杂程度
    private DLinkNode head, tail;
    // 初始化
    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        head = new DLinkNode();
        tail = new DLinkNode();
        head.next = tail;
        tail.pre = head;
    }

    public int get(int key) {
        // 获取结点
        DLinkNode node = cache.get(key);
        // 不存在返回-1
        if (node == null) return -1;
        // 移动到头部,LRU被使用过后就会移动，尾部的结点有限淘汰
        moveToHead(node);
        return node.val;
    }

    public void put(int key, int value) {
        if (!cache.containsKey(key)) {
            // 新加一个结点
            DLinkNode node = new DLinkNode(key, value);
            cache.put(key, node);
            addToHead(node);
            size++;
            // 如果超过了容量就删除一个结点(链表尾部就是最近最久未使用的结点)
            // 这里是延迟删除的，不是容量一超过就马上删除
            if (size > capacity) {
                DLinkNode tail = removeTail();
                cache.remove(tail.key);
                --size;
            }
        }else {
            // 在map里面就是已经有过的结点，直接命中，移动结点就好了
            DLinkNode node = cache.get(key);
            node.val = value;
            moveToHead(node);
        }
    }

    public void moveToHead(DLinkNode node){
        removeNode(node);
        addToHead(node);
    }

    public void removeNode(DLinkNode node){
        DLinkNode next = node.next;
        DLinkNode pre = node.pre;
        pre.next = next;
        next.pre = pre;
    }

    public void addToHead(DLinkNode node) {
        node.pre = head;
        node.next = head.next;
        head.next.pre = node;
        head.next = node;
    }

    public DLinkNode removeTail(){
        DLinkNode res = tail.pre;
        tail = res;
        res.next = null;
        return res;
    }
}
```
