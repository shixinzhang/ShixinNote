###[点击查看 Java 集合框架深入理解 系列](http://blog.csdn.net/u011240877/article/category/6447444)， - ( ゜- ゜)つロ 乾杯~ 
------------------
>上篇文章我们介绍了 [哈希相关概念：哈希 哈希函数 冲突解决 哈希表](http://blog.csdn.net/u011240877/article/details/52940469)，这篇文章我们来深入了解下使用频率很高的 HashMap ！


![这里写图片描述](http://img.blog.csdn.net/20161027225946876)

##什么是 HashMap

HashMap 是一个采用哈希表实现的键值对集合，继承自 [AbstractMap](http://blog.csdn.net/u011240877/article/details/52949046)，实现了 [Map 接口](http://blog.csdn.net/u011240877/article/details/52929523) 。

HashMap 具有以下特点：

- 提供了 Map 全部的方法
- 允许空键和空值
- 元素是无序的，而且顺序会不定时改变
- 插入、获取的时间复杂度基本是 O(1)（前提是有适当的哈希函数，让元素分布在均匀的位置）
- 

除了不允许 null 并且同步，Hashtable 几乎和他一样。



![这里写图片描述](http://img.blog.csdn.net/20161027225853859)



图解HashMap和HashSet的内部工作机制

https://mp.weixin.qq.com/s?__biz=MzAwNjA3NDMyOA==&mid=2659762989&idx=2&sn=7c53d42dcc54cdaa8c90c98d65d39fc8&chksm=806e999ab719108c3c0dece8f0484bf00e4589e93c8bb1ca42c86e0c09f27711f1b332b2873c&scene=0&key=&ascene=7&uin=&devicetype=android-23&version=26031b31&nettype=WIFI




自己之前转的，参考完删掉

http://blog.csdn.net/u011240877/article/details/45025623
http://blog.csdn.net/u011240877/article/details/45025073
http://blog.csdn.net/u011240877/article/details/45025079

##Thanks

http://blog.csdn.net/eson_15/article/details/51158865

https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html

https://github.com/GeniusVJR/LearningNotes/blob/master/Part2/JavaSE/HashMap%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90.md

http://www.nowamagic.net/librarys/veda/detail/1273

http://www.nowamagic.net/librarys/veda/detail/1202

