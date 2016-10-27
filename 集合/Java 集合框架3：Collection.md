

##Collection 接口

###Collection 作为集合的一个根接口，定义了一组对象和它的子类需要实现的 15 个方法：
![这里写图片描述](http://img.blog.csdn.net/20161009234816861)

###对集合的基础操作，比如 :
 - `int size() `
  - 获取元素个数
-  `boolean isEmpty()`
  -  是否个数为 0
-   `boolean contains(Object element)`
  -   是否包含指定元素
-    `boolean add(E element)`
  -    添加元素，成功时返回 true
-     `boolean remove(Object element)`
  -     删除元素，成功时返回 true
-      `Iterator<E> iterator()`
  -      获取迭代器

###还有一些操作整个集合的方法，比如 : 
- `boolean containsAll(Collection<?> c)`
  -  是否包含指定集合 c 的全部元素
-  `boolean addAll(Collection<? extends E> c)`
  - 添加集合 c 中所有的元素到本集合中，如果集合有改变就返回 true
-   `boolean removeAll(Collection<?> c)`
  - 删除本集合中和 c 集合中一致的元素，如果集合有改变就返回 true   
-    `boolean retainAll(Collection<?> c)`
  - 保留本集合中 c 集合中没有的元素，如果集合有改变就返回 true   
-     `void clear()`
  - 删除所有元素    

###还有对数组操作的方法：
-  `Object[] toArray()`
  -   返回一个包含集合中所有元素的数组
-   `<T> T[] toArray(T[] a)`
  -   返回一个包含集合中所有元素的数组，运行时根据集合元素的类型指定数组的类型

###在 JDK 8 以后，Collection 接口还提供了从集合获取连续的或者并发的流：
- `Stream<E> stream()`
- `Stream<E> parallelStream()`

####[点击这里](https://docs.oracle.com/javase/tutorial/collections/streams/index.html)了解流 Stream.


##遍历 Collection 的几种方式：
1. `for-each`语法


    	Collection<Person> persons = new ArrayList<Person>();
    	for (Person person : persons) { 
    		System.out.println(person.name);  
    	}  
2.  使用 `Iterator` 迭代器


		Collection<Person> persons = new ArrayList<Person>();
		Iterator iterator = persons.iterator();
    	while (iterator.hasNext) { 
    		System.out.println(iterator.next);  
    	}  

3. 使用 `aggregate operations ` 聚合操作

		Collection<Person> persons = new ArrayList<Person>();
    	persons
			.stream()
			.forEach(new Consumer<Person>() {  
        		@Override  
        		public void accept(Person person) {  
            		System.out.println(person.name);  
        		}  
    	}); 
##Aggregate Operations 聚合操作

### 在 JDK 8 以后，推荐使用聚合操作对一个集合进行操作。聚合操作通常和 lambda 表达式结合使用，让代码看起来更简洁（因此可能更难理解）。下面举几个简单的栗子：

1.使用流来遍历一个 ShapesCollection，然后输出红色的元素：

    myShapesCollection.stream()
    	.filter(e -> e.getColor() == Color.RED)
    	.forEach(e -> System.out.println(e.getName()));

2.你还可以获取一个并发流（parallelStream），当集合元素很多时使用并发可以提高效率：

    myShapesCollection.parallelStream()
    	.filter(e -> e.getColor() == Color.RED)
    	.forEach(e -> System.out.println(e.getName()));							
3.聚合操作还有很多操作集合的方法，比如说你想把 Collection 中的元素都转成 String 对象，然后把它们 连起来：

	String joined = elements.stream()
    	.map(Object::toString)
    	.collect(Collectors.joining(", "));
看起来是不是非常简洁呢！

####聚合操作还有很多功能，这里仅做介绍，想要了解更多可以查看[Aggregate Operations 官方指引](https://docs.oracle.com/javase/tutorial/collections/streams/index.html)。

##Iterator 迭代器

###在[Java 集合解析：Iterator](http://blog.csdn.net/u011240877/article/details/52743564) 和 [Java 集合解析：ListIterator](http://blog.csdn.net/u011240877/article/details/52752589) 我介绍了 Collection 的迭代器 Iterator 以及用于 List 的迭代器 ListIterator。

###结合 Collection 和 Iterator 可以实现一些复用性很强的方法，比如这样：

	public static void filter(Collection<?> c) {
    	for (Iterator<?> it = c.iterator(); it.hasNext(); )
        	if (!condition(it.next()))
        	    it.remove();
	}

###这个 filter 方法是多态的，可以用于所有 Collection 的子类、实现类。 这个例子说明了使用 Java 集合框架我们可以很随便就写出 “优雅，可拓展，复用性强” 的代码~


##总结

###Collection 接口是类集框架的基础之一。
###它创建所有类集都将拥有的 15 个核心方法。
###因为几乎所有集合类集都实现了 Collection接口，所以熟悉它对于清楚地理解框架是必要的。
###接下来将逐步了解集合框架的各个子接口及实现类。

##Thanks

https://docs.oracle.com/javase/tutorial/collections/intro/index.html

https://docs.oracle.com/javase/tutorial/collections/interfaces/collection.html