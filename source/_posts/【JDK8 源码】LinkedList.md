title: 【JDK8 源码】LinkedList
date: 2019-10-22 12:44:15
categories: default
tags: []
---
# 三、LinkedList

底层是由双向链表实现的

```java
private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

相当于一个结构体

```c++
struct node{
    e item;
    node *next;
    node *prev;
}node;
```

有了头结点，其他的数据我们都可以获取得到了



## 3.1 构造方法

```java
/**
     * Constructs an empty list.
     */
    public LinkedList() {
    }

    /**
     * Constructs a list containing the elements of the specified
     * collection, in the order they are returned by the collection's
     * iterator.
     *
     * @param  c the collection whose elements are to be placed into this list
     * @throws NullPointerException if the specified collection is null
     */
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
```

一共两个构造方法，一个空构造方法

```java
public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);

        Object[] a = c.toArray(); //将c转换为数组
        int numNew = a.length;//存储a的长度
        if (numNew == 0)//如果没有元素返回false
            return false;

        Node<E> pred, succ; //创建节点
        if (index == size) {//如果index刚好等于链表大小，存入最后一个
            succ = null;
            pred = last;
        } else {
            succ = node(index);
            pred = succ.prev;
        }

        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
            Node<E> newNode = new Node<>(pred, e, null);
            if (pred == null)
                first = newNode;
            else
                pred.next = newNode;
            pred = newNode;
        }

        if (succ == null) {
            last = pred;
        } else {
            pred.next = succ;
            succ.prev = pred;
        }

        size += numNew;
        modCount++;
        return true;
    }
```

## 3.2 add(int index, E element)

- add实际上就是往链表的最后添加元素

```java
public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
```

## 3.3 remove方法

```java
public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);//删除元素
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {//删除元素，看看元素是否在里面
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }


//*********************unlike******************
 /**
     * Unlinks non-null node x.
     */
    E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }
```

![1571559251341](/home/wangx/笔记/img/1571559251341.png)

相当于这张图

## 3.4 get方法

```java
public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
 /**
     * Returns the (non-null) Node at the specified element index.
     */
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {//下标小于长度的一般就从头遍历
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)//否则从尾部开始遍历
                x = x.prev;
            return x;
        }
    }
```

## 3.5 set方法

> set方法和get方法其实差不多，**根据下标来判断是从头遍历还是从尾遍历**

```java
public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }
```

## 3.6 总结

- LInkedLIst的实质是一个双向链表
- LikedList是非线程安全的
- **查询多用ArrayList，增删多用LinkedList。**

