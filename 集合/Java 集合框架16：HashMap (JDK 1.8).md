###[点击查看 Java 集合框架深入理解 系列](http://blog.csdn.net/u011240877/article/category/6447444)， - ( ゜- ゜)つロ 乾杯~ 
------------------
>上篇文章我们介绍了 [哈希相关概念：哈希 哈希函数 冲突解决 哈希表](http://blog.csdn.net/u011240877/article/details/52940469)，这篇文章我们来根据 JDK 1.8 源码，深入了解下使用频率很高的 HashMap 。
>
>读完本文你将了解：
>1.HashMap 的两种底层数据结构
>2.HashMap 的关键方法（添加、扩容、变形）
>3.HashMap 的特点


![这里写图片描述](http://img.blog.csdn.net/20161027225946876)

##什么是 HashMap

HashMap 是一个采用**哈希表**实现的键值对集合，继承自 [AbstractMap](http://blog.csdn.net/u011240877/article/details/52949046)，实现了 [Map 接口](http://blog.csdn.net/u011240877/article/details/52929523) 。

![这里写图片描述](http://img.blog.csdn.net/20161027225853859)

当发生 哈希冲突（碰撞）的时候，HashMap 采用**拉链法**（不熟悉这个概念的同学可以 [点这里了解](http://blog.csdn.net/u011240877/article/details/52940469)）进行解决，因此 HashMap 的底层实现是 **数组+链表**，如下图 所示：


![这里写图片描述](http://img.blog.csdn.net/20161027004004320)

##HashMap 的特点
结合平时使用，可以了解到 HashMap 大概具有以下特点：

- 底层是链表数组 + 红黑树
- 提供了 Map 全部的方法
- key 用 Set 存放，所以想做到 **key 不允许重复**，key 对应的类需要重写 hashCode 和 equals 方法
- 允许空键和空值
- 元素是无序的，而且顺序会不定时改变
- 插入、获取的时间复杂度基本是 O(1)（前提是有适当的哈希函数，让元素分布在均匀的位置）
- 遍历整个 Map 需要的时间与 桶(数组) 的长度成正比（因此初始化时 HashMap 的容量不宜太大）
- 两个关键因子：初始容量、加载因子

除了不允许 null 并且同步，Hashtable 几乎和他一样。

##HashMap 的初始容量和加载因子

由于 HashMap 扩容开销很大（需要创建新数组、重新哈希、分配等等），因此与扩容相关的两个因素：

- 容量：数组的数量
- 加载因子：决定了 HashMap 中的元素占有多少比例时扩容

成为了 HashMap 最重要的部分之一，它们决定了 HashMap 什么时候扩容。

HashMap 的默认加载因子为 0.75，这是在时间、空间两方面均衡考虑下的结果：

- 加载因子太大的话桶太多，遍历时效率变低
- 太小的话频繁 rehash，导致性能降低

当设置初始容量时，需要提前考虑 Map 中可能有多少对键值对，设计合理的加载因子，尽可能避免进行扩容。

如果存储的键值对很多，干脆设置个大点的容量，这样可以少扩容几次。

##HashMap 的扩容


##HashMap 的 13 个成员变量
![这里写图片描述](http://img.blog.csdn.net/20161030092127363)

1.默认初始容量：16，必须是 2 的整数次方

    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; 

2.默认加载因子的大小：0.75，可不是随便的，结合时间和空间效率考虑得到的

    static final float DEFAULT_LOAD_FACTOR = 0.75f;

3.最大容量： 2^ 30 次方

    static final int MAXIMUM_CAPACITY = 1 << 30;

4.当前 HashMap 修改的次数，这个变量用来保证 *fail-fast* 机制

    transient int modCount;

5.阈值，下次需要扩容时的值，等于 容量*加载因子

    int threshold;

6.树形阈值：JDK 1.8 新增的，当使用 树 而不是列表来作为桶时使用。必须必 2 大

    static final int TREEIFY_THRESHOLD = 8;

7.非树形阈值：也是 1.8 新增的，扩容时分裂一个树形桶的阈值（？不是很懂 - -），要比 TREEIFY_THRESHOLD 小

    static final int UNTREEIFY_THRESHOLD = 6;

8.树形最小容量：桶可能是树的哈希表的最小容量。至少是 TREEIFY_THRESHOLD 的 4 倍，这样能避免扩容时的冲突

    static final int MIN_TREEIFY_CAPACITY = 64;

9.缓存的 键值对集合（另外两个视图：keySet 和 values 是在 [AbstractMap](http://blog.csdn.net/u011240877/article/details/52949046) 中声明的）

    transient Set<Map.Entry<K,V>> entrySet;

10.哈希表中的链表数组

    transient Node<K,V>[] table;

11.键值对的数量

    transient int size;

12.哈希表的加载因子

    final float loadFactor;

##HashMap 的关键方法

###1. HashMap 的 4 个构造方法

	//创建一个空的哈希表，初始容量为 16，加载因子为 0.75
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }

	//创建一个空的哈希表，指定容量，使用默认的加载因子
    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

	//创建一个空的哈希表，指定容量和加载因子
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
		//根据指定容量设置阈值
        this.threshold = tableSizeFor(initialCapacity);
    }

	//创建一个内容为参数 m 的内容的哈希表
    public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
    }

其中第三种构造方法调用了 `tableSizeFor(int)` 来根据指定的容量设置阈值，这个方法经过若干次无符号右移、求异运算，得出最接近指定参数 cap 的 2 的 N 次方容量。假如你传入的是 5，返回的初始容量为 8 。

    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }

第四种构造方法调用了 `putMapEntries()`，这个方法用于向哈希表中添加整个集合：

    final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
        int s = m.size();
        if (s > 0) {
			//数组还是空，初始化参数
            if (table == null) { 
                float ft = ((float)s / loadFactor) + 1.0F;
                int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                         (int)ft : MAXIMUM_CAPACITY);
                if (t > threshold)
                    threshold = tableSizeFor(t);
            }
			//数组不为空，超过阈值就扩容
            else if (s > threshold)
                resize();
            for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
                K key = e.getKey();
                V value = e.getValue();
				//先经过 hash() 计算位置，然后复制指定 map 的内容
                putVal(hash(key), key, value, false, evict);
            }
        }
    }

###2.HashMap 中的链表节点

前面提到，HashMap 的底层数据结构之一（JDK 1.8 前单纯是）就是链表数组：


    transient Node<K,V>[] table;

每一个链表节点如下：

	//实现了 Map.Entry 接口
    static class Node<K,V> implements Map.Entry<K,V> {
		//哈希值，就是位置
        final int hash;
		//键
        final K key;
		//值
        V value;
		//指向下一个几点的指针
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
				//Map.Entry 相等的条件：键相等、值相等、个数相等、顺序相等
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }


###3.HashMap 中的添加操作

下文用“桶”来指代要数组，每个桶都对应着一条链表：

	//添加指定的键值对到 Map 中，如果已经存在，就替换
    public V put(K key, V value) {
		//先调用 hash() 方法计算位置
        return putVal(hash(key), key, value, false, true);
    }

    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
		//如果当前 哈希表内容为空，新建，n 指向最后一个桶的位置，tab 为哈希表另一个引用
		//resize() 后续介绍
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
		//如果要插入的位置没有元素，新建个节点并放进去
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
			//如果要插入的桶已经有元素，替换
			// e 指向被替换的元素
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
			//p 指向要插入的桶第一个 元素的位置，如果 p 的哈希值、键、值和要添加的一样，就停止找，e 指向 p
                e = p;
            else if (p instanceof TreeNode)
			//如果不一样，而且当前采用的还是 JDK 8 以后的树形节点，调用 putTreeVal 插入
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
			//否则还是从传统的链表数组查找、替换

				//遍历这个桶所有的元素
                for (int binCount = 0; ; ++binCount) {
					//没有更多了，就把要添加的元素插到后面得了
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
						//当这个桶内链表个数大于等于 8，就要树形化
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
					//如果找到要替换的节点，就停止，此时 e 已经指向要被替换的节点
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
			//存在要替换的节点
            if (e != null) {
                V oldValue = e.value;
				//替换，返回
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
		//如果超出阈值，就得扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }

插入过程中涉及到几个其他关键的方法 ：

- hash():计算对应的位置
- resize():扩容
- putTreeVal():树形节点的插入
- treeifyBin():树形化容器

HashMap 在 JDK1.8 新增树形化相关的内容比较多，另外写篇介绍，接下来先介绍传统的 HashMap 相关内容。


###4.HashMap 中的哈希函数 hash() 

HashMap 中通过将传入键的 hashCode 进行无符号右移 16 位，然后进行按位异或，得到这个键的哈希值。

    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }

看看源码里的注释怎么说的：

> Computes key.hashCode() and spreads (XORs) higher bits of hash to lower.  Because the table uses power-of-two masking, sets of hashes that vary only in bits above the current mask will always collide. (Among known examples are sets of Float keys holding consecutive whole numbers in small tables.)  So we apply a transform that spreads the impact of higher bits downward. There is a tradeoff between speed, utility, and quality of bit-spreading. Because many common sets of hashes are already reasonably distributed (so don't benefit from spreading), and because we use trees to handle large sets of collisions in bins, we just XOR some shifted bits in the cheapest possible way to reduce systematic lossage, as well as to incorporate impact of the highest bits that would otherwise never be used in index calculations because of table bounds.
     
大概意思就是：

由于哈希表的容量都是 **2 的 N 次方**，在当前，元素的 hashCode() 在很多时候下低位是相同的，这将导致冲突（碰撞），因此 1.8 以后做了个移位操作：将元素的 hashCode() 和自己右移 16 位后的结果求异或。

由于 int 只有 32 位，无符号右移 16 位相当于把高位的一半移到低位：

![这里写图片描述](http://img.blog.csdn.net/20161030215139729)

举个栗子：

![这里写图片描述](http://img.blog.csdn.net/20161031000139666)
（图片来自 http://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/）

这样可以避免只靠低位数据来计算哈希时导致的冲突，计算结果由高低位结合决定，可以避免哈希值分布不均匀。

而且，采用位运算效率更高。

###5.HashMap 中的初始化/扩容方法 resize()

每次添加时会比较当前元素个数和阈值：

		//如果超出阈值，就得扩容
        if (++size > threshold)
            resize();

扩容：

	   final Node<K,V>[] resize() {
		//复制一份当前的数据
        Node<K,V>[] oldTab = table;
		//保存旧的元素个数、阈值
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
			//新的容量为旧的两倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
				//如果旧容量小于等于 16，新的阈值就是旧阈值的两倍
                newThr = oldThr << 1; // double threshold
        }
		//如果旧容量为 0 ，并且旧阈值>0，说明之前创建了哈希表但没有添加元素，初始化容量等于阈值
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
			//旧容量、旧阈值都是0，说明还没创建哈希表，容量为默认容量，阈值为 容量*加载因子
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
		//如果新的阈值为 0 ，就得用 新容量*加载因子 重计算一次
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
		//更新阈值
        threshold = newThr;
		//创建新链表数组，容量是原来的两倍
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
		//接下来就得遍历复制了
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
					//旧的桶置为空
                    oldTab[j] = null;
					//当前 桶只有一个元素，直接赋值给对应位置
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
						//如果旧哈希表中这个位置的桶是树形结构，就要把新哈希表里当前桶也变成树形结构
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { //保留旧哈希表桶中链表的顺序
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
						//do-while 循环赋值给新哈希表
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }

几个关键的点：

- 新初始化哈希表时，容量为默认容量，阈值为 容量*加载因子
- 已有哈希表扩容时，容量、阈值均翻倍
- 如果之前这个桶的节点类型是树，需要把新哈希表里当前桶也变成树形结构
- 复制给新哈希表中需要重新索引（rehash），这里采用的计算方法是
 - e.hash & (newCap - 1)，等价于 e.hash % newCap

结合扩容源码可以发现扩容的确开销很大，需要迭代所有的元素，rehash、赋值，还得保留原来的数据结构。

所以在使用的时候，最好在初始化的时候就指定好 HashMap 的长度，尽量避免频繁 resize()。

###6.HashMap 的获取方法 get()

HashMap 另外一个经常使用的方法就是 get(key)，返回键对应的值:

如果 HashMap 中包含一个键值对 k-v 满足：

	(key==null ? k==null : key.equals(k))

就返回值 v，否则返回 null;

    public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
		//tab 指向哈希表，n 为哈希表的长度，first 为指定位置处的桶头节点
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
			//如果桶第一个元素就相等，直接返回
            if (first.hash == hash &&
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
			//否则就得慢慢遍历找
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
					//如果是树形节点，就调用树形节点的 get 方法
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
					//do-while 遍历链表的所有节点
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }

get 方法比较简单，时间复杂度一般跟链表长度有关，因此哈希算法越好，元素分布越均匀，get() 方法就越快，不然遍历一条长链表，太慢了。

不过在 JDK 1.8 以后 HashMap 新增了红黑树节点，优化这种极端情况下的性能问题。


##总结

1.HashMap 有那么多优点，

也有个缺点：**不是同步的**。

当多线程并发访问一个 哈希表时，需要在外部进行同步操作，否则会引发数据不同步问题。

你可以选择加锁，也可以考虑用 `Collections.synchronizedMap` 包一层，变成个线程安全的 Map：

	Map m = Collections.synchronizedMap(new HashMap(...));

最好在初始化时就这么做。

2.HashMap 三个视图返回的迭代器都是 *fail-fast* 的：如果在迭代时使用**非迭代器方法**修改了 map 的内容、结构，迭代器就会报 *ConcurrentModificationException* 的错。

3.当 HashMap 中有大量的元素都存放到同一个桶中时，这时候哈希表里只有一个桶，这个桶下有一条长长的链表，这个时候 HashMap 就相当于一个单链表，假如单链表有 n 个元素，遍历的时间复杂度就是 O(n)，完全失去了它的优势。

针对这种情况，JDK 1.8 中引用了 红黑树（时间复杂度为 O(logn)） 优化这个问题。

4.为什么哈希表的容量一定要是 2的整数次幂?

>首先，capacity 为 2的整数次幂的话，**h&(length-1) 就相当于对 length 取模**，提升了计算效率；
>
>其次，capacity 为 2 的整数次幂的话，为偶数，这样 capacity-1 为奇数，奇数的最后一位是 1，这样便保证了 h&(capacity-1) 的最后一位可能为 0，也可能为 1（这取决于h的值），即**与后的结果可能为偶数，也可能为奇数，这样便可以保证散列的均匀性**；
>
>而如果 capacity 为奇数的话，很明显 capacity-1 为偶数，它的最后一位是 0，这样 h&(capacity-1) 的最后一位肯定为 0，即**只能为偶数，这样任何 hash 值都只会被散列到数组的偶数下标位置上，这便浪费了近一半的空间**。
>
>因此，容量取 2 的整数次幂，是为了使不同 hash 值发生碰撞的概率较小，尽可能促使元素在哈希表中均匀地散列。
>
>摘自： https://github.com/GeniusVJR/LearningNotes/blob/master/Part2/JavaSE/HashMap%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90.md

5.HashMap 允许 key-value 为 null，同时他们都保存在**第一个桶**中。

添加时先调用 hash()：

    public V put(K key, V value) {
		//先调用 hash() 方法计算位置
        return putVal(hash(key), key, value, false, true);
    }

而在计算哈希值时，如果为 null 就直接返回 0 ，说明了这一点：

    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }

6.HashMap 中 equals() 和 hashCode() 有什么作用？

HashMap 的添加、获取时需要通过 key 的 hashCode() 进行 hash()，然后计算下标 ( n-1 & hash)，从而获得要找的同的位置。

当发生冲突（碰撞）时，利用 key.equals() 方法去链表或树中去查找对应的节点。

7.你知道 hash 的实现吗？为什么要这样实现？

>在 JDK 1.8 的实现中，是通过 **hashCode() 的高16位异或低16位实现的**：`(h = k.hashCode()) ^ (h >>> 16)`。
>
>主要是从**速度、功效、质量** 来考虑的，这么做可以在桶的 n 比较小的时候，保证高低 bit 都参与到 hash 的计算中，同时位运算不会有太大的开销。
>
> 摘自：http://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/

##下篇文章介绍 HashMap 在 JDK 1.8 新增的红黑树结构，点击查看

##Thanks

https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html

http://blog.csdn.net/eson_15/article/details/51158865

http://blog.csdn.net/q291611265/article/details/46763053

http://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/

https://github.com/GeniusVJR/LearningNotes/blob/master/Part2/JavaSE/HashMap%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90.md

http://www.nowamagic.net/librarys/veda/detail/1273


