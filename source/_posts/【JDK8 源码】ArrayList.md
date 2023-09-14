title: 【JDK8 源码】ArrayList
date: 2019-10-20 08:25:00
categories: Java
tags: []
---
# 一、ArrayList



![TIM图片20191020150648.png](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2019/10/921275579.png)

我们可以发现ArrayLIst的底层是一个数组，其中友谊个扩容的概念，正式因为他，所以ArrayList，才能实现动态增长

## 1.1 构造方法

----------

```java
/**
* Constructs an empty list with the specified initial capacity.
*
* @param  initialCapacity  the initial capacity of the list
* @throws IllegalArgumentException if the specified initial capacity
*         is negative
*/
public ArrayList(int initialCapacity) {
if (initialCapacity > 0) {
this.elementData = new Object[initialCapacity]; //指定了容量就初始化为指定容量的数组
} else if (initialCapacity == 0) {
this.elementData = EMPTY_ELEMENTDATA;//如果指定的为0，就返回一个空集合
} else {
throw new IllegalArgumentException("Illegal Capacity: "+
initialCapacity);
}
}
```

```java
	/**
		 * Constructs an empty list with an initial capacity of ten.
		 */
		public ArrayList() {
			this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
		}
	//空构造函数就返回一个默认的空集合
```

## 1.2 add方法

直接添加元素

- 确认list容量，尝试容量+1，看看有无必要
- 添加元素

### 1.2.1 add(E e)



```java
/**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
//**************************第一步***********************************
    private void ensureCapacityInternal(int minCapacity) {
            if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
                minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
                //想要得到最小的容量(防止浪费资源)
            }

            ensureExplicitCapacity(minCapacity);
        }
//****************************第二步********************************
//确定明确的容量
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)//如果要的最小容量比数组的容量还要大的话，那么就调用grow()来扩容
            //比如当添加第11个元素时,minCapacity为11，数组长度只有10，那么就需要扩容了
            grow(minCapacity);
    }
//*******************************第三步******************************
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);//添加oldCapacity的一半  >>1相当于除以2，相当于增加到原来的0.5倍
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
    //扩容完成后调用copyOf()方法,将原来的元素复制到新的数组里面
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
//*******************第四步，我们看看copyOf方法********************

    @SuppressWarnings("unchecked")
    public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }

    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
            @SuppressWarnings("unchecked")
            T[] copy = ((Object)newType == (Object)Object[].class)
                ? (T[]) new Object[newLength]
                : (T[]) Array.newInstance(newType.getComponentType(), newLength);
            System.arraycopy(original, 0, copy, 0,
                             Math.min(original.length, newLength));
            return copy;
        }
```

到此我们可以知道add(E e)的实现了，步骤如下:

- 首先去检查数组容量是否足够
- 不够就扩容到原来**1.5**倍
- 第一次扩容，如果容量小于minCapacity，就将容量扩容为minCapacity
- 足够：直接添加
- 不足够：扩容

### 1.2.2 add(int index,E element)

步骤：

- 检查角标
- 空间检查，如果有需要进行扩容
- 插入元素


```java
/**
     * Inserts the specified element at the specified position in this
     * list. Shifts the element currently at that position (if any) and
     * any subsequent elements to the right (adds one to their indices).
     *
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);//检查角标是否越界
		//扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);//调用arraycopy()方法进行插入
        elementData[index] = element;
        size++;
    }
```

我们由此可知，关于扩容的ArrayList的add方法底层其实都是**arraycopy()**来实现的，下面我们看看arraycopy()方法

```java
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

我们可以发现这个方法是由C/C++来实现的，并不是由Java实现的

## 1.3 get方法

```java
public E get(int index) {    rangeCheck(index);    return elementData(index);}
//************************检查角标*******************************
private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
//***********************返回元素********************
E elementData(int index) {
        return (E) elementData[index];
    }
```

- 检查角标
- 返回元素

## 1.4 set 方法

```java
public E set(int index, E element) {
        rangeCheck(index);//检查角标,出错抛出异常

        E oldValue = elementData(index);//保存旧值
        elementData[index] = element;//将值替代
        return oldValue;//返回旧值
    }
```

- 检查角标
- 替代元素
- 返回旧值

## 1.5 remove方法

```java
public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
```

- 检查角标
- 可用容量+1
- 删除元素
- 计算出移动的个数,进行移动
- 设置为null，让GC进行回收

## 1.6 总结

- ArrayList是 **基于动态数组实现的**，在 增删时候，**需要数组拷贝赋值**
- ArrayList的默认初始化容量是10，每次扩容是增加原来容量的一般，就是将现在容量增大1.5倍
- 删除元素不会减少容量，若希望减少要调用trimToSize()

```java
/**
     * Trims the capacity of this <tt>ArrayList</tt> instance to be the
     * list's current size.  An application can use this operation to minimize
     * the storage of an <tt>ArrayList</tt> instance.
     */
    public void trimToSize() {
        modCount++;
        if (size < elementData.length) {
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```



- 他不是线程安全的。他能存放null值

