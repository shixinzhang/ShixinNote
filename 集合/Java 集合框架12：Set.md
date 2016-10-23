

>终于把 List 常用的几种容器介绍完了，接下来开始 Set 的相关介绍。

##什么是 Set

为什么删除Set中元素要求这个类要继承Comparable接口，难道实现equals不就行了吗？
举个例子吧
src

Collection<Name> c = new HashSet<Name>();
c.add(new Name("f1","l1"));
boolean isSuccess = c.remove(new Name("f1", "l1")); //？
为什么要实现compareTo方法，java API为什么这样实现？

为什么和hash相关的集合，为什么要实现hashcode方法？
还是上面的例子Name不仅要实现compareTo方法，还要实现hashCode方法啊， 就是为了快速查找吗？


这个网站栈的数据结构文章很棒

http://www.nowamagic.net/librarys/veda/detail/2298

https://docs.oracle.com/javase/tutorial/collections/interfaces/set.html