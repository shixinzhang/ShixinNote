>杜牧有云：学非探其花，要自拔其根。

#Java SE 核心技术巩固

##一. Java 基础
####1. Java 面向对象

####2. Java 字符串

####3. Java 泛型

####4. Java 装箱拆箱

####5. Java 反射

####6. Java 枚举

####7. Java 异常


##二. Java 集合源码解析
###什么是集合？
- 集合，或者叫容器，是一个包含多个元素的对象；
- 集合可以对数据进行存储，检索，操作；
- 它们可以把许多个体组织成一个整体：
 - 比如一副扑克牌（许多牌组成的集合）;
 - 比如一个电话本（许多姓名和号码的映射）。

###什么是集合框架？
####集合框架是一个代表、操作集合的统一架构。所有的集合框架都包含以下几点：
- **接口**：表示集合的抽象数据类型。接口允许我们操作集合时不必关注具体实现，从而达到“多态”。在面向对象编程语言中，接口通常用来形成规范。
- **实现类**：集合接口的具体实现，是重用性很高的数据结构。
- **算法**：用来根据需要对实体类中的对象进行计算，比如查找，排序。
 - 同一种算法可以对不同的集合实现类进行计算，这是利用了“多态”。
 - 重用性很高。

####不仅 Java，其他语言也有一些集合框架，比如 C艹 的 STL（标准模板库），Smalltalk 的集合层次结构。不同于他们陡峭的学习曲线，Java 集合框架设计的更加合理，学习起来更加轻松。

###使用集合框架有什么好处呢?

####使用 Java 集合框架能有以下几点好处：
- **编码更轻松**：Java 集合框架为我们提供了方便使用的数据结构和算法，让我们不用从头造轮子，直接操心上层业务就好了。
- **代码质量更上一层楼**：Java 集合框架经过几次升级迭代，数据结构和算法的性能已经优化地很棒了。由于是针对接口编程，不同实现类可以轻易互相替换。这么优雅的设计，省下你自己磨练多少工夫，恩？！
- **减少学习新 API 的成本**：过去每个集合 API 下还有子 API 来对 API 进行操作，你得学好几层才能知道怎么使用，而且还容易出错。现在好了!有了标准的 Java 集合框架，每个 API 都继承自己顶层 API，只负责具体实现，一口气学 5 个集合，不费劲！
- **照猫画虎也容易多了**：由于顶层接口已经把基础方法都定义好了，你只要实现接口，把具体实现方法填好，再也不用操心架构设计。


###Java 集合框架主要结构图

![这里写图片描述](http://img.blog.csdn.net/20161008225645176)

####如上图所示，Java 的集合主要按两种接口分类：Collection, Map.

####[1. Iterator](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B61%EF%BC%9AIterator.md)

####[2. ListIterator *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B62%EF%BC%9AListIterator.md)

####[3. Collection](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B63%EF%BC%9ACollection.md)

####[4. AbstractCollection](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B64%EF%BC%9AAbstractCollection.md)

####[5. AbstractList](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B65%EF%BC%9AAbstractList.md)

####[6. List *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B66%EF%BC%9AList.md)

####[7. ArrayList *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B67%EF%BC%9AArrayList.md)

####[8. AbstractSequentialList](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B68%EF%BC%9AAbstractSequentialList.md)

####[9. Queue *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B69%EF%BC%9AQueue.md)

####[10. Deque *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B610%EF%BC%9ADeque.md)

####[11. LinkedList *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B611%EF%BC%9ALinkedList.md)

####[12. Vector *](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B612%EF%BC%9AVector.md)

####[13. Stack](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B613%EF%BC%9AStack.md)

####[14. Map](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%EF%BC%9AMap.md)

####[15. AbstractMap](https://github.com/shixinzhang/ShixinJavaSECore/blob/master/%E9%9B%86%E5%90%88/Java%20%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B615%EF%BC%9AAbstractMap.md)

###三. Java I/O

###四. Java 并发编程 

###五. Java 网络编程

##About me

张拭心，shixinzhang，Java Android 菜鸟

GitHub: https://github.com/shixinzhang/ShixinJavaSECore

CSDN：http://blog.csdn.net/u011240877