>大家好，这里是 《 Java 刨根问底》 栏目，去疑惑，无死角。

>首先提个问题：
>Java 泛型的作用是什么？泛型擦除是什么？泛型一般用在什么场景？

>如果这个问题你答不上来，那这篇文章可能就对你有些价值。

读完本文你将了解到：

[TOC]

##什么是泛型
泛型是Java SE 1.5的新特性，泛型的本质是**参数化类型**，也就是说所操作的数据类型被指定为一个参数。


这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。

###泛型类

###泛型方法

###泛型接口


	/**
	 * <header>
	 *      Description: 泛型类
	 * </header>
	 * <p>
	 *      Author: shixinzhang
	 */
	public class GenericClass<F> {
	    private F mContent;
	
	    public GenericClass(F content){
	        mContent = content;
	    }
	
	    /**
	     * 泛型方法
	     * @return
	     */
	    public F getContent() {
	        return mContent;
	    }
	
	    public void setContent(F content) {
	        mContent = content;
	    }
	
	    /**
	     * 泛型接口
	     * @param <T>
	     */
	    public interface GenericInterface<T>{
	        void doSomething(T t);
	    }
	}


用泛型编写的Java程序和普通的Java程序基本相同，只是多了一些参数化的类型同时少了一些类型转换。实际上泛型程序也是首先被转化成一般的不带泛型的 Java 程序后再进行处理的，编译器自动完成了从 Generic Java 到普通 Java 的翻译。

在Java中，泛型是 Java 编译器的概念，Java 虚拟机运行时对泛型基本一无所知。

##为什么引入泛型

根据《Java编程思想 （第4版）》中的描述，泛型出现的动机在于：

>有许多原因促成了泛型的出现，而最引人注意的一个原因，就是为了创建容器类。

集合容器的意义在于保存不同类型的元素，使用泛型可以XXXXXXX。

	public interface Collection<E> extends Iterable<E> {
	
	    Iterator<E> iterator();
	
	    Object[] toArray();
	
	    <T> T[] toArray(T[] a);
	    boolean add(E e);
	
	    boolean remove(Object o);

	    boolean containsAll(Collection<?> c);
	
	    boolean addAll(Collection<? extends E> c);
	
	    boolean removeAll(Collection<?> c);
	}	

ArrayList 源码：

	transient Object[] elementData; // non-private to simplify nested class access  
        
这里为什么数组中的元素数据类型是Object？

实际上引入泛型的主要目标有以下几点：

- 类型安全
 -  泛型的主要目标是提高 Java 程序的类型安全。
 -  使得java代码可以编译时期检查出因java类型导致的可能在运行时抛出ClassCastException异常。
 -  符合越早出错代价越小原则。
- 消除强制类型转换
 - 泛型的一个附带好处是，消除源代码中的许多强制类型转换。
 - 这使得代码更加可读，并且减少了出错机会。
- 潜在的性能收益
 - 泛型为较大的优化带来可能。在泛型的初始实现中，编译器将强制类型转换（没有泛型的话，程序员会指定这些强制类型转换）插入生成的字节码中。但是更多类型信息可用于编译器这一事实，为未来版本的 JVM 的优化带来可能。
 - 由于泛型的实现方式，支持泛型（几乎）不需要 JVM 或类文件更改。所有工作都在编译器中完成，编译器生成类似于没有泛型（和强制类型转换）时所写的代码，只是更能确保类型安全而已。

##泛型的规则


- 泛型的类型参数只能是类类型（包括自定义类），不能是简单类型。
- 同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。
- 泛型的类型参数可以有多个。
- 泛型的参数类型可以使用extends语句，例如。习惯上称为“有界类型”。
- 泛型的参数类型还可以是通配符类型。例如Class

##泛型的类型参数

##泛型的通配符

有时候希望传入的类类型有一个指定的范围，从而可以进行一些允许的操作，这时候就是通配符边界登场的时候了。泛型的边界分两种：上界和下界。 

泛型中有三种通配符形式：

	1. <?>
	2. <? extends E> extends关键字声明了类型的上界，表示参数化的类型可能是所指定的类型，或者是此类型的子类。
	3. <? super E>

接下来进行介绍，并且分析与类型参数形式的区别和联系。

大部分情况下，这种限制是好的，但这使得一些理应正确的基本操作都无法完成，比如交换两个元素的位置，看代码：

	public static void swap(DynamicArray<?> arr, int i, int j){
	    Object tmp = arr.get(i);
	    arr.set(i, arr.get(j));
	    arr.set(j, tmp);
	}

这个代码看上去应该是正确的，但Java会提示编译错误，两行set语句都是非法的。不过，借助带类型参数的泛型方法，这个问题可以这样解决：

	private static <T> void swapInternal(DynamicArray<T> arr, int i, int j){
	    T tmp = arr.get(i);
	    arr.set(i, arr.get(j));
	    arr.set(j, tmp);
	}
	
	public static void swap(DynamicArray<?> arr, int i, int j){
	    swapInternal(arr, i, j);
	}

swap可以调用swapInternal，而带类型参数的swapInternal可以写入。Java容器类中就有类似这样的用法，公共的API是通配符形式，形式更简单，但内部调用带类型参数的方法。


##通配符比较

两种通配符形式<? super E>和<? extends E>也比较容易混淆，我们再来比较下。

它们的目的都是为了使方法接口更为灵活，可以接受更为广泛的类型。

- <? super E>用于灵活写入或比较，使得对象可以写入父类型的容器，使得父类型的比较方法可以应用于子类对象。
- <? extends E>用于灵活读取，使得方法可以读取E或E的任意子类型的容器对象。

简单总结来说：

- <?>和<? extends E>用于实现更为灵活的读取，它们可以用类型参数的形式替代，但通配符形式更为简洁。
- <? super E>用于实现更为灵活的写入和比较，不能被类型参数形式替代。

##泛型的类型擦除


将泛型代码转换成普通的Java代码的过程是由编译器（如javac）来完成的，虚拟机并不负责这一任务。当编译器对带有泛型的java代码进行编译时，它会去执行**类型检查**和**类型推断**，然后生成普通的不带泛型的字节码，这种普通的字节码可以被一般的Java虚拟机接收病执行，这中技术就叫做擦除（erasure）。 

实际上无论你是否使用泛型，集合框架中存放对象的数据类型都是Object，这一点不仅仅从源码中可以看到，通过反射也可以看到。泛型作用域编译期，到运行时就已经被擦除了。

	List<String> strings = new ArrayList<>();
    List<Integer> integers = new ArrayList<>();
    System.out.println(strings.getClass() == integers.getClass());//true

上面代码输出结果并不是预期的false，而是true。其原因就是泛型的擦除。


泛型中没有逻辑上的父子关系，如List<Number>并不是List<Integer>的父类。两者擦除之后都是List，所以形如

    /**
     * 两者并不是方法的重载。擦除之后都是同一方法，所以编译不会通过。
     * 擦除之后：
     * 
     * void m(List numbers){}
     * void m(List strings){} //编译不通过，已经存在相同方法签名
     */
    void m(List<Number> numbers) {

    }

    void m(List<String> strings) {

    }

##泛型的使用场景

当类中要操作的引用数据类型不确定的时候，一开始定义 Object 来完成扩展，后来使用泛型来完成扩展，同时保证安全性。

##总结



http://www.runoob.com/java/java-generics.html

http://www.kutear.com/post/java/2016-09-04-think_in_java_15

http://blog.csdn.net/u011240877/article/details/47702745

这个人写了好几篇泛型的

http://blog.csdn.net/crave_shy/article/details/50822354

泛型面试题
http://blog.csdn.net/mariofei/article/details/19137753#comments

##Thanks

https://docs.oracle.com/javase/tutorial/java/generics/index.html

http://www.journaldev.com/1663/java-generics-example-method-class-interface#java-generics-bounded-type-parameters

http://blog.csdn.net/crave_shy/article/details/50822354

http://mp.weixin.qq.com/s/te9K3alu8P8jRUUU2AkO3g

http://mp.weixin.qq.com/s/TNxaOK2hBDzRtlNUlGxHcQ

http://www.infoq.com/cn/articles/cf-java-generics
