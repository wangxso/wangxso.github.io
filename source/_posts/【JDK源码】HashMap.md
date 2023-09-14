title: 【JDK源码】HashMap
date: 2019-11-06 12:45:00
categories: Java
tags: []
---
# HashMap

hashmap 的 **数据结构基础** 就是一张**哈希表**,**使用拉链法**来解决哈希冲突

[解决哈希冲突的三种方法（拉链法、开放地址法、再散列法）](https://blog.csdn.net/qq_32595453/article/details/80660676)

HashMap的java语言实现基础是 **数组+（链表 or 红黑树）**

![image-20191105164948556.png](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2019/11/3356721251.png)
## 0.实现与继承

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable
```

HashMap 继承来自 AbstractMap类

这个类提供了Map接口的基本实现

```java
public abstract class AbstractMap<K,V> implements Map<K,V> 
```

同时实现了Map接口,Cloneable,Serializable接口为标识接口

## 1. 参数

```java
//默认初始容量，必须是2的幂，这里是2^4=16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
//最大的容量，默认为2^30
static final int MAXIMUM_CAPACITY = 1 << 30;
//没有在构造器中指定时，默认的填充因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;
//当桶(bucket)上的结点数大于这个值时会转成红黑树
static final int TREEIFY_THRESHOLD = 8;
//当(bucket)上的结点数小于这个值时会还原成链表
static final int UNTREEIFY_THRESHOLD = 6;
//桶中结构转化为红黑树对应的数组的最小大小，如果当前容量小于它，就不会将链表转化为红黑树，而是用resize()代替	
static final int MIN_TREEIFY_CAPACITY = 64;
//存储元素的数组，大小总是2的幂
transient java.util.HashMap.Node<K,V>[] table;
//存放具体元素的集合
transient Set<Map.Entry<K,V>> entrySet;
//集合目前的大小,并非数组的length,即当前Map存储了多少个元素
transient int size;
//计数器，版本号，每次修改就会+1
transient int modCount;
//临界值,当实际节点个数超过临界值(容量*填充因子)时，就会进行扩容
int threshold;
//填充因子
final float loadFactor;
```

- JDK8中加入了红黑树结构，当链表因为hash冲突超过8的时候就会将链表转换为红黑树
- HashMap数组的大小只能是2的幂次方整数
- 当 length > threshold的时候就会进行扩容 threshold = table.length* loadFactor
- 在HashMap中，**哈希桶数组table的长度length大小必须为2的n次方(一定是合数)**，这是一种非常规的设计，常规的设计是把桶的大小设计为素数。相对来说素数导致冲突的概率要小于合数；**同时也是使用位运算来优化hash%len的前提** 

## 2. 构造函数

```java
/**
*带参构造函数，初始化初始容量和填充因子
*
**/
public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)//传入的初始容量值小于0，抛出异常
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)//传入的值大于Integer的最大值，补偿措施
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))//填充因子出现问题，抛异常
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;//为填充因子赋值
        this.threshold = tableSizeFor(initialCapacity); //为传入的初始容量做处理
    //tableSizeFor 是返回一个比给定整数大的2的幂次方整数
    }

//假设传入一个65
static final int tableSizeFor(int cap) {
    	//n = 64（1000000）
        int n = cap - 1;
    	// n >>> 1 = 0100000
    	//    0100000 | 1000000 = 1100000
    	// 可得 n = 00000000 01111111（2进制） 63
    	// >>>表示无符号右移，也叫逻辑右移，即若该数为正，则高位补0，而若该数为负数，则右移后高位同样补0
        n |= n >>> 1;// 0011
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;//返回+1 即 64
    }
```

```java
//带参构造函数，不指定填充因子，使用默认的填充因子0.75
public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

/**
     * HashMap无参构造函数
     */
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }
//带参构造函数，参数是一个Map的实例，作用是把Map转换为HashMap，也可以理解为是拷贝
public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
    }
```



## 3. hash函数

```java
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    /**
    *key为null则指定hash值为0
    *其他则获取其hashCode方法 然后进行异或运算
    *1. h  = key.hashCode() 取hashCode值
    *2. h^(h >>> 16) 高位参与运算
    *3.  (n - 1) & hash     第三步: 结果对table的数组长度进行取模，得到具体的存储索引位置 
    */
    }
```

- hash函数是HashMap的核心

- 异或计算是两个二进制进行计算，比如000111，100000则是100111，即不同为1，相同为0

- 向右移动16位，数值小的情况下，就是000000....与0进行异或几乎就不需要进行异或、、

- 为什么进行异或计算？**加大hash码低位随机性，是的分布更加均匀**，从而增加存储数组下标的随机性 和 均匀性 ，最终使得减少hash冲突

- 为什么取模运算？ **因为进行二次计算的值可能不在数组的索引范围**，所以结果需要对数组长度进行mod运算，得到具体的数组索引位置，实际应用中**使用hashcode与数组长度-1进行与操作**(效果等于hash%len)。 

- **Java 8 中的取模运算不集成在hash方法中，取模运算出现在真正需要用到计算数组的索引位置时用到，比如put方法，resize方法中** 

![image-20191105192405910.png](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2019/11/1023903107.png)

  ## 4. put系列函数

  ```java
  /**
  	 * default方法，不对外公开，只允许构造函数和putAll内部调用,有两种模式
       * putMapEntries的作用：将别的集合元素填充到当前集合中，可以看做是一个拷贝函数
       * 该方法有两个参数m和evict,evict主要在putVal得到使用
       * @param m 参数集合
       * @param evict evict为false则代表创建模式，用于HashMap的构造。如果为true,则用于putAll
       */
  final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
          int s = m.size(); //获取s的大小
          if (s > 0) {
              //如果当前集合的table为空，即当前集合是空集合，则初始化参数
              if (table == null) { // pre-size
                  float ft = ((float)s / loadFactor) + 1.0F; 
                  int t = ((ft < (float)MAXIMUM_CAPACITY) ? //判断ft是否小于集合最大支持的容量，是就赋值
                           (int)ft : MAXIMUM_CAPACITY);
                  if (t > threshold) //判断t是否大于临界值(容量 * 填充因子)
                      threshold = tableSizeFor(t);//修正t为2的幂次方的整数
              }
              //当前不为空，但是大小大于临界值
              else if (s > threshold)
                  resize();//扩容函数
              for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {//循环变量参数集合，放进当前集合中
                  K key = e.getKey();
                  V value = e.getValue();
                  putVal(hash(key), key, value, false, evict);
              }
          }
      }
  
  //公有方法，实际调用的是putMapEntries方法，但是evict值为true,即代表不是创建模式，即非通过构造函数调用
  //其实就是putMapEntries方法的对外公开模式
  public void putAll(Map<? extends K, ? extends V> m) {
          putMapEntries(m, true);
      }
  
  ```

  - putMapEntries是对内的方法，而putAll是对外的方法



```java
//onlyIfAbsent为true就不改变现在的值
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    
        Node<K,V>[] tab; Node<K,V> p; int n, i;
    //如果table为null或tap为空（长度为0）就扩容
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
    //确定插入的位置,算法是(n-1) & hash  在n=2的幂次方的时候，相当于取模
    //不存在这个元素直接新建加入
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
    //在i位置发生碰撞有两种情况,1.key值相等，替换value值,
    //2.key值不一样，又分为两种
    //2.1 存储在i位置的链表中，2.2、存储在红黑树中（大于8的时候）
        else {
            Node<K,V> e; K k;
            //第一种情况
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //2.2中情况
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            //2.1的情况
            else {
                //遍历链表
                for (int binCount = 0; ; ++binCount) {
                    //到链表尾都没找到key值相同的节点就新建一个结点插入
                    if ((e = p.next) == null) {
                       //创建结点插入链尾
                        p.next = newNode(hash, key, value, null);
                        //超过链表预设的长度8就转换为红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //找到key，就替换原来的
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            
            //如果e值不为空就更新值
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

- 在没有这个结点的时候会直接添加它
- 如果存在key相等的就直接替换值即可
- 发生hash冲突
  - 存储在链表里面的，就循环链表找到key值相等的替换，如果没有就在链尾添加结点
  - 存储在红黑树里面的直接将元素插入红黑树

```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table; //保存旧的table
        int oldCap = (oldTab == null) ? 0 : oldTab.length;//获取旧的table长度
        int oldThr = threshold;//获取旧的table的临界值
        int newCap, newThr = 0;//初始化新的容量和临界值
        if (oldCap > 0) {//当旧的容量>0时
            if (oldCap >= MAXIMUM_CAPACITY) {//旧的容量大于最大的容量
                threshold = Integer.MAX_VALUE;//临界值为Integer的最大值
                return oldTab;//直接返回旧的table,无法扩容
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                //如果旧容量的两倍小鱼最大容量，并且比初始默认容量(16)大的话就变成原来的两倍
                newThr = oldThr << 1; // double threshold
        }
    //旧的临界值大于0，就去旧的临界值为容量
    ///当table没初始化时，threshold持有初始容量。
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            //初始化的临界值为0的话就把新的容量赋值为默认的（16），
            //新的临界值为默认填充因子×默认容量,即0.75*16
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
    //新临界值为0
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
    // 初始化table
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            //table不为空，把oldTab的结点全部rehash到新的table里面
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {// 要取的元素不为空
                    oldTab[j] = null;
                    //若节点是单个节点，直接在 newTab　中进行重定位
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    //若是树节点，要对红黑树rehash
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    //若为链表，进行链表的rehash
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            //根据算法 e.hash & oldCap判断结点rehash之后是否发生改变，最高位为0就是不改变
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            //最高位为1就改变
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {//原来bucket位置的尾指针不为空
                            loTail.next = null;//链表最后的null
                            newTab[j] = loHead;//链表头指针放在新bucket的j处
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            // rehash 后结点新的位置已定位原基础加上oldCap
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }

```

### 5. 为什么使用0.75作为负载因子？

负载因子就是Entry数组的填充的程度，填充的程度过低会使得哈希冲突比较严重并且空间利用率低，过高则会导致一定查询效率的减少。

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```



### 6. 什么情况下转化为红黑树？

一般都会认为转化为红黑树为链表结点大于8的时候会进行，然后小于6会退化为链表。但是还有一个重要的条件就是数组的长度，如果数组长度大于64的话，才会树化，如果小于的话，就会优先对于数组进行扩容。这样的好处就是可以增加一定的查询效率，避免红黑树左旋右旋等调整带来的一系列开销。

```java
   	static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;
```

### 

###  7. 看下HashMap的结点是怎么样的。

```java
static class Node<K,V> implements Map.Entry<K,V> {
        // 哈希值
    	final int hash;
        // 键值
    	final K key;
        // 值
    	V value;
    	// 采取拉链法，链表指向下一个
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }
		// 重写hashCode，为了重写equals方法
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
```

比较简单的一个结构，就是一个单链表，同时重写了equals方法。

### 8. Put函数

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
    	// 初始没有，就赋默认值，Cap=16，threshold=12，loadfactor=0.75
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
    	// 数组中不存在该hash值对应的内容，新建一个结点即可
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            // key存在，只要更新value即可
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                // 这个是为了LinkedHashMap后续操作设计的，在HashMap中没有实现具体方法
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
    	// 超过了threshold，扩容
        if (++size > threshold)
            resize();
    	// 这个是为了LinkedHashMap后续操作设计的，在HashMap中没有实现具体方法
        afterNodeInsertion(evict);
        return null;
    }
```

具体步骤如下：

- 先判断table是否为空或者初始cap为0，赋上默认值。
- 然后如果想要存的结点key的hash在数组中不存在，则新建结点。
- 如果存在，判断该节点是否为树的结点
  - 是，将新的结点传入红黑树，红黑树会自动调整位置。
  - 否，查找到链表尾部，同时判断新加上一个结点是否会超过8，如果超过8，树化，没有直接添加在链表尾部。
- 更新结点里面的值Value。
- 判断当前的数组的大小是否大于threshold，大于就扩容。

## End