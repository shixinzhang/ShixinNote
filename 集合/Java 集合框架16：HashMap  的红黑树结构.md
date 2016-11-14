###[点击查看 Java 集合框架深入理解 系列](http://blog.csdn.net/u011240877/article/category/6447444)， - ( ゜- ゜)つロ 乾杯~ 
------------------
>上篇文章我们介绍了 [HashMap 的主要特点和关键方法源码解读]()，这篇文章我们介绍 HashMap 在 JDK1.8 新增树形化相关的内容。
>
>读完本文你将了解：
>1.HashMap 的新增底层数据结构--红黑树
>2.HashMap 的红黑树相关的方法（添加、扩容、变形）

##传统 HashMap 的缺点

当 HashMap 中有大量的元素都存放到同一个桶中时，这时候哈希表里只有一个桶，这个桶下有一条长长的链表，这个时候 HashMap 就相当于一个单链表，假如单链表有 n 个元素，遍历的时间复杂度就是 O(n)，完全失去了它的优势。

针对这种情况，JDK 1.8 中引用了 红黑树（时间复杂度为 O(logn)） 优化这个问题

##HashMap 在 JDK 1.8 中新增的数据结构 -- 树形节点

在上面的几个关键方法里可以看到，HashMap 还有另外一种节点：TreeNode，它是 1.8 新增的，属于数据结构中的 **红黑树**：

在Java 8中，如果一个bucket中碰撞冲突的元素超过某个限制(默认是8)，则使用红黑树来替换链表，从而提高速度。

##HashMap 在 JDK 1.8 中新增的操作：树形化

##面试题

HashMap里面的hash映射？如何实现根据Key的hashcode找到下标？HashMap做了哪些优化？


##Thanks

http://openjdk.java.net/jeps/180

http://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/

http://www.importnew.com/14417.html

