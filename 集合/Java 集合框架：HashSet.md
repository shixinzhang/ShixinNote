###[点击查看 Java 集合框架深入理解 系列](http://blog.csdn.net/u011240877/article/category/6447444)， - ( ゜- ゜)つロ 乾杯~ 

-------------

##
值得注意的是 HashSet 迭代时会遍历全部 entries 跟 容量。

因此如果初始化时容量太大，不仅浪费空间，还耽误时间。

不过初始容量太小也不行，那样需要多次扩容，浪费时间。

如果你不表明初始容量的话，默认初始容量为 16。容量是 int 类型的，总是 2 的 N 次方。

下面这个例子初始化一个 初始容量为 64 的 HashSet：

    Set<String> s = new HashSet<String>(64);

HashSet 另外一个关键的参数就是 **加载因子**（load factor），它也影响 HashSet 占用的空间。




##Thanks
https://docs.oracle.com/javase/tutorial/collections/implementations/set.html

