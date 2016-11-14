根据《Java编程思想 （第4版）》中的描述，泛型出现的动机在于：

>有许多原因促成了泛型的出现，而最引人注意的一个原因，就是为了创建容器类。

ArrayList 源码：

	transient Object[] elementData; // non-private to simplify nested class access  
        
这里为什么数组中的元素数据类型是Object？

实际上无论你是否使用泛型，集合框架中存放对象的数据类型都是Object，这一点不仅仅从源码中可以看到，通过反射也可以看到。泛型作用域编译期，到运行时就已经被擦除了，而反射是获取一个对象的运行时数据信息，有兴趣的同学可以自行了解，这里不再详细介绍。

http://www.runoob.com/java/java-generics.html

http://www.kutear.com/post/java/2016-09-04-think_in_java_15

http://blog.csdn.net/u011240877/article/details/47702745

这个人写了好几篇泛型的

http://blog.csdn.net/crave_shy/article/details/50822354