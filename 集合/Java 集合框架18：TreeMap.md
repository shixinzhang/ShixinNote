http://blog.csdn.net/mazhimazh/article/details/19961017

reeMap里面实现得最出彩的地方还是红黑树的部分，当然，还有其他一两个比较有意思的方法，其问题还经常被作为一些面试的问题来讨论，后续的文章也会针对这部分进行一些分析。


- 基于红黑树（Red-Black tree）的 NavigableMap 实现。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。
- 键值对是红黑树结构，可以保证键的排序和唯一性
- 此实现不是同步的

须为TreeMap提供排序方案

①根据其键的自然顺序进行排序（自定义对象须实现Comparable接口并重写compare方法） 
②根据创建映射时提供的Comparator进行排序，具体取决于使用的构造方法。

http://shmilyaw-hotmail-com.iteye.com/blog/1836431

http://blog.csdn.net/qq_28261343/article/details/52627545