>集合，知道常用集合类的继承结构，这个类的底层实现结构（数组，链表），这个类对应的数据结构原理（栈，队列，堆，哈希，红黑树等），一些重要内部方法的工作机制（扩容，如何哈希），一些常用公开方法的使用



####Java 提供的 集合类都在 Java.utils 包下，

![这里写图片描述](http://img.blog.csdn.net/20161005150455194)

####其中包含了许多类，Collection, List, Set, Map, Queue, 等等。他们之间的关系如下面几张类图所示：

![这里写图片描述](http://img.blog.csdn.net/20161005150203595)

![这里写图片描述](http://img.blog.csdn.net/20161005150216822)

![这里写图片描述](http://img.blog.csdn.net/20161005150233112)

####虽然这几张图繁简程度不同，但是可以看到，Java 集合主要分为两类：Collection 和 Map.

##Collection 
####Collection 是一个接口，不能实例化。它继承自 Iterable ，而 Iterable 也是一个接口，它定义了一个 iterator() 方法用于获取一个 Iterator，帮助子类进行遍历。
![这里写图片描述](http://img.blog.csdn.net/20161005151550722)

####Collection 规定子类至少实现 2 个构造函数，一个没有参数，一个有参数，比如这样


####Collection 定义了数据集合的 15 个基本操作，

![这里写图片描述](http://img.blog.csdn.net/20161005151216687)

###Collection 的直接子类是 **List** 和 **Set**,它们也是接口，在父类的基础上进行了一些拓展。

##Map
#### Map

##面试题
http://blog.csdn.net/u011240877/article/details/47148619