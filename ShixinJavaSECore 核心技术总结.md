##Why 

1.的确不熟悉，巩固基础
2.阅读第三方源码前练手，体验各个模块设计时的结构

##Java 资料
http://www.runoob.com/java/java-vector-class.html

##面试

有研究过部分JDK源码，比如常用的集合类如HashMap/Hashtable、ArrayList/LinkedList、Vector等，还有Java5之后的并发包JUC如concurrentHashMap、Executor框架、CopyOnWrite容器等。自己很欣赏Java巧妙的垃圾回收机制，看过周志明的《深入理解Java虚拟机》，因此对JVM相关的知识有所掌握……

###把JVM的结构和类加载原理说下
把虚拟机运行时包含的几个数据区和执行引擎画了下，包括方法区、虚拟机栈、本地方法栈、堆和程序计数器，然后介绍每个区域有什么作用，最后讲ClassLoader的类加载机制，还顺便说了下双亲委派机制

###Java的GC机制的巧妙之处在哪里？
从两个方面说下自己的理解：一是Java的内存分配原理与C/C++不同，C/C++每次采用malloc或new申请内存时都要进行brk和mmap等系统调用，而系统调用发生在内核空间，每次都要中断进行切换，这需要一定的开销，而Java虚拟机是先一次性分配一块较大的空间，然后每次new时都在该空间上进行分配和释放，减少了系统调用的次数，节省了一定的开销，这有点类似于内存池的概念；二是有了这块空间过后，如何进行分配和回收就跟GC机制有关了，然后我详细介绍了GC原理、画图表示年轻代（Eden区和Survival区）、年老代、比例分配及为啥要这样分代回收（我认为巧妙就在于这里），有了GC基本结构后，我又详述了下GC是具体如何进行内存分配和垃圾回收的。

##面经/面试题
http://blog.csdn.net/u011240877/article/details/41677477

http://blog.csdn.net/lanxuezaipiao/article/details/40184329
http://blog.csdn.net/lanxuezaipiao/article/details/40054675
http://blog.csdn.net/lanxuezaipiao/article/details/16753743

http://blog.csdn.net/tzs_1041218129/article/details/52355867

http://blog.csdn.net/lanxuezaipiao/article/details/41822683