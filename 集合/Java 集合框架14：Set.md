###[点击查看 Java 集合框架深入理解 系列](http://blog.csdn.net/u011240877/article/category/6447444)， - ( ゜- ゜)つロ 乾杯~ 

>终于把 List 常用的几种容器介绍完了，接下来开始 Set 的相关介绍。

------------------
##数学中的 集合

![这里写图片描述](http://img.blog.csdn.net/20161023150623111)

（图片可爱了点，但很贴合生活 0.0 ）

数学中的**集合**表示由一个或多个元素所构成的叫做集合。若x是集合A的元素，则记作x∈A。

集合中元素的特性：

1. **确定性**：给定一个集合，任何对象是不是这个集合的元素是确定的了
2. **互异性**：集合中的元素一定是不同的
3. **无序性**：集合中的元素没有固定的顺序.

##Java 集合框架中的集合 Set

![这里写图片描述](http://img.blog.csdn.net/20161023145804436)

Java 集合中的 Set 表示一个元素不能重复的集合。
更准确的说，一个 set 中不存在两个元素 e1,e2, 使得 e1.equals(e2) 成立，**最多只能有一个 null**　元素。

它和数学中的 **集合** 概念一致。

![这里写图片描述](http://img.blog.csdn.net/20161023150846831)

Set 直接继承自 [Collection 接口](http://blog.csdn.net/u011240877/article/details/52773577) ，和 [List](http://blog.csdn.net/u011240877/article/details/52802849), [Queue](http://blog.csdn.net/u011240877/article/details/52860924) 在同一级别。

Set 接口 在 Collection 接口的基础上添加了一些额外的规定，比如对 add, equals, hashCode 方法的规定。

**注意**：

- Set 的实现类在添加元素时需要确定不重复
- 如果集合中的元素是**可变的**，在元素修改时也需要确保不重复
- 不是所有实现类都允许元素为 null

##Set 接口定义的方法

![这里写图片描述](http://img.blog.csdn.net/20161023152412617)

大多数都是 Collection 中已经定义的。

###1.添加
	
添加指定元素 e 到 set 中：

	//如果 set 中没有其他元素 e2 满足： 
	//(e == null ? e2 == null : e.equals(e2)) == true
	//才可以添加成功，返回 true
	//否则添加失败，并且返回 false
    boolean add(E e);

添加一个集合中的所有元素到 set 中：

	//集合中的每个元素也要确保上面的条件
	//如果目标集合也是个 set，那这个操作后本 set 会变成这个两个 set 的并集
	//这个方法没有规定添加过程中，目标集合 c 如果修改了怎么办??
	//只要添加一个元素就返回 true
    boolean addAll(Collection<? extends E> c);

添加方法 和 构造函数 双剑合璧，确保 set 中没有重复元素

###2.删除

删除指定元素：

	//删除一个元素 e ,当 e 满足还是这个公式：
	//(o == null ? e == null : o.equals(e)) == true
	//当包含并且删除后，返回 true
    boolean remove(Object o);

删除指定集合中的全部元素：
	
	//如果操作的集合也是个 set
	//这个操作会把本地 set 变成两个集合的差集
    boolean removeAll(Collection<?> c);

保留指定集合的全部元素，剩下都删掉：
	
	//修改后变成两个 set 的交集
    boolean retainAll(Collection<?> c);

删除全部：

    void clear();

###3.查询
是否包含指定元素：

	//判断标准还是这个 
	//(o == null ? e == null : o.equals(e)) == true
    boolean contains(Object o);

是否包含集合中的全部元素：

	//如果指定集合也是个 set，这个 set 得是本地 set 的子集才返回 true
    boolean containsAll(Collection<?> c);

获取迭代器：
	
	//这个时候 set 中的元素一般都是无序的
	//除非某个特点的实现类在插入时就维持顺序
    Iterator<E> iterator();

    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, Spliterator.DISTINCT);
    }

其他两个常规状态查询：

    boolean isEmpty();

    int size();

###4.转成数组

toArray()，数组和 Collection 的桥梁：

	//返回一个包含所有元素的数组
	//如果集合中的元素在迭代器遍历后变成有序的
	//变成的数组也是有序的
    Object[] toArray();

返回一个包含 set 中全部元素的数组
这个数组 a 的类型和 set　元素类型一致，就直接把元素放到数组　ａ　中并返回　
注意，是从头开始放，如果经过的位置有元素，就覆盖了
否则新建一个数组返回

    <T> T[] toArray(T[] a);

###5.最关键的两个方法,比较，哈希

对比指定 set 和本地 set 元素的顺序，内容是否一致：
	
	//这个方法确定了不同的 set 实现类也能相等，只要内容一致
    boolean equals(Object o);

返回这个集合的哈希值：
	
	//一个集合的哈希值是由集合中全部元素的哈希值之和决定的
	// null 集合的哈希值为 0
	//这确保了 equlas 方法的结果一般与 hashCode 结果一致
	//但 hashCode 相等的两个结合 不一定 equals
    int hashCode();

##Set 的实现类

###Java 集合框架中包含三种普通用途的 Set 实现类：

1. HashSet
 - 底层实现是 **哈希表** ，是性能最好的实现类
 - 然而它不能保证元素迭代时的顺序
2. TreeSet
 - 底层实现是 **红黑树** ，比 HashSet 慢一点
 - 根据元素的值进行排序
3. LinkedHashSet
 - 底层实现是 **链表实现的哈希表**
 - 有序的，输出顺序与输入顺序一致

HashSet 比 TreeSet 要快的多,但是没有后者的排序功能。

如果你需要使用 SortedSet 接口，或者需要元素有序，就使用 TreeSet,否则就用 HashSet。

大多数情况下 HashSet 比 TreeSet 使用频率高。

LinkedHashSet 介于这两者之间，有序的，而且效率也跟 HashSet 差不多。

###另外两种特殊用途的 Set 实现：

- EnumSet
 - 底层实现是 **枚举**
 - EnumSet 中的元素必须是同一种枚举类型 
- CopyOnWriteArraySet
 - 底层实现是 **写时拷贝的数组**
 - 所有修改性的操作（add,set,remove 等等），都会拷贝出一份进行操作
 - 主要用于遍历
 - 数组实现，所以修改时性能比较低

EnumSet 提供了对所有枚举类型的遍历方法 EnumSet.range，比如有一个定义一周五天的枚举，你可以这样进行遍历：

    for (Day d : EnumSet.range(Day.MONDAY, Day.FRIDAY))
        System.out.println(d);

EnumSet 还提供了类型安全的替换操作，比如这样：

    EnumSet.of(Style.BOLD, Style.ITALIC)

##总结

1.Set 的不同实现类也能相等，只要内容相同

2.Set 中元素如何存储（实现不重复的存储）的呢？
  
- 数量很大时的添加，一个个比较效率太低；
- 哈希算法: 不去一一比较，计算新添加元素的哈希值，提高很多效率。

3.哈希算法

- 根据 hashCode() 得到位置，看这个值上有没有存储元素，如果没有就是不重复的，直接存进去；
- 如果有存储元素，再调用 equals 方法比较是否相同：
 - 如果相同，后面的元素就不能添加进来；
 - 否则，都存储。 （不建议如此，一般要求： hashCode() 结果 与 equals() 结果一致）

所以想要实现一个 Set 中存放的自定义实体类不重复，需要重写该实体类的 equals() 和 hashCode() 方法！！！

##Set 使用场景 ： 去重

加入你有一个集合 c, 里面有很多重复的元素，下面一句话就可以帮你去除重复元素：

    Collection<Type> noDups = new HashSet<Type>(c);

它使用了 [Colloection　接口](http://blog.csdn.net/u011240877/article/details/52773577) 标准的传参构造函数。

下面这个 removeDups 方法使用 LinkedHashSet 实现了一个通用的方法，同时保证在删除重复元素的时候保留原始集合的顺序：

    public static <E> Set<E> removeDups(Collection<E> c) {
    	return new LinkedHashSet<E>(c);
    }

或者再 JDK 8 以后，你可以用聚合操作实现上述功能：

    c.stream()
    .collect(Collectors.toSet()); // no duplicates

##Map与Set的关系

Set集合的特点是不能存储重复元素，不能保持元素插入时的顺序，且key值最多允许有一个null值。
由于Map中的key与Set集合特点相同，所以如果将Map中的value值当作key的附属的话，所有的key值就可以组成一个value集合。
来看一下两个集合的实现类图：
http://blog.csdn.net/mazhimazh/article/details/17876641

##Thanks

http://baike.baidu.com/link?url=Pgz8A5Z1zy1tH-2PX3dHtIQzeTq9JXXKKWidKtsU8eKWdG9Nw_w8KgebbnUXO3JnbqV8JJhmo9eUhJHBIlI1Hq

https://docs.oracle.com/javase/tutorial/collections/interfaces/set.html

https://docs.oracle.com/javase/tutorial/collections/interfaces/set.html

http://www.cnblogs.com/skywang12345/p/3311136.html