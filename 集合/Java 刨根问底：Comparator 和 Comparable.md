读完本文你将了解到：

[TOC]

Java 中为我们提供了两种比较机制：Comparable 和 Comparator，他们之间有什么区别呢？今天来了解一下。

##Comparable 自然排序

Comparable 在 java.lang 包下，是一个接口，内部只有一个方法 compareTo()：

	public interface Comparable<T> {
	    public int compareTo(T o);
	}

Comparable 可以让实现它的类的对象进行比较，具体的比较规则是按照 compareTo 方法中的规则进行。这种顺序称为 **自然顺序**。

compareTo 方法的返回值有三种情况：

- e1.compareTo(e2) > 0 即 e1 > e2
- e1.compareTo(e2) = 0 即 e1 = e2
- e1.compareTo(e2) < 0 即 e1 < e2

注意：
>1.由于 null 不是一个类，也不是一个对象，因此在重写 compareTo 方法时应该注意 e.compareTo(null) 的情况，即使 e.equals(null) 返回 false，compareTo 方法也应该主动抛出一个空指针异常 NullPointerException。
>
>2.Comparable 实现类重写 compareTo 方法时一般要求 e1.compareTo(e2) == 0 的结果要和 e1.equals(e2) 一致。这样将来使用 SortedSet 等**根据类的自然排序进行排序**的集合容器时可以保证保存的数据的顺序和想象中一致。
>

有人可能好奇上面的第二点如果违反了会怎样呢？

举个例子，如果你往一个 SortedSet 中先后添加两个对象 a 和 b，a b 满足 (!a.equals(b) && a.compareTo(b) == 0)，同时也没有另外指定个 Comparator，那当你添加完 a 再添加 b 时会添加失败返回 false, SortedSet 的 size 也不会增加，因为在 SortedSet 看来它们是相同的，而 SortedSet 中是不允许重复的。

实际上所有实现了 Comparable 接口的 Java 核心类的结果都和 equlas 方法保持一致。
实现了 Comparable 接口的 List 或则数组可以使用 `Collections.sort()` 或者 `Arrays.sort()` 方法进行排序。

实现了 Comparable 接口的对象才能够直接被用作 SortedMap (SortedSet) 的 key，要不然得在外边指定 Comparator 排序规则。

因此自己定义的类如果想要使用有序的集合类，需要实现 Comparable 接口，比如：

	**
	 * description: 测试用的实体类 书, 实现了 Comparable 接口，自然排序
	 * <br/>
	 * author: shixinzhang
	 * <br/>
	 * data: 10/5/2016
	 */
	public class BookBean implements Serializable, Comparable {
	    private String name;
	    private int count;
	
	
	    public BookBean(String name, int count) {
	        this.name = name;
	        this.count = count;
	    }
	
	    public String getName() {
	        return name;
	    }
	
	    public void setName(String name) {
	        this.name = name;
	    }
	
	    public int getCount() {
	        return count;
	    }
	
	    public void setCount(int count) {
	        this.count = count;
	    }
	
	    /**
	     * 重写 equals
	     * @param o
	     * @return
	     */
	    @Override
	    public boolean equals(Object o) {
	        if (this == o) return true;
	        if (!(o instanceof BookBean)) return false;
	
	        BookBean bean = (BookBean) o;
	
	        if (getCount() != bean.getCount()) return false;
	        return getName().equals(bean.getName());
	
	    }
	
	    /**
	     * 重写 hashCode 的计算方法
	     * 根据所有属性进行 迭代计算，避免重复
	     * 计算 hashCode 时 计算因子 31 见得很多，是一个质数，不能再被除
	     * @return
	     */
	    @Override
	    public int hashCode() {
	        //调用 String 的 hashCode(), 唯一表示一个字符串内容
	        int result = getName().hashCode();
	        //乘以 31, 再加上 count
	        result = 31 * result + getCount();
	        return result;
	    }
	
	    @Override
	    public String toString() {
	        return "BookBean{" +
	                "name='" + name + '\'' +
	                ", count=" + count +
	                '}';
	    }
	
	    /**
	     * 当向 TreeSet 中添加 BookBean 时，会调用这个方法进行排序
	     * @param another
	     * @return
	     */
	    @Override
	    public int compareTo(Object another) {
	        if (another instanceof BookBean){
	            BookBean anotherBook = (BookBean) another;
	            int result;
	
	            //比如这里按照书价排序
	            result = getCount() - anotherBook.getCount();     
	
	          //或者按照 String 的比较顺序
			  //result = getName().compareTo(anotherBook.getName());
	
	            if (result == 0){   //当书价一致时，再对比书名。 保证所有属性比较一遍
	                result = getName().compareTo(anotherBook.getName());
	            }
	            return result;
	        }
	        // 一样就返回 0
	        return 0;
	    }


上述代码还重写了 equlas(), hashCode() 方法，自定义的类想要进行比较时都要重写这些方法。

**后面重写 compareTo 时，要判断某个相同时对比下一个属性，把所有属性都比较一次。**



Comparable 接口属于 Java 集合框架的一部分。

##Comparator 定制排序

Comparator 在 java.util 包下，也是一个接口，JDK 1.8 以前只有两个方法：

	public interface Comparator<T> {
	    
	    public int compare(T lhs, T rhs);
	
	    public boolean equals(Object object);
	}
	
JDK 1.8 以后又新增了很多方法：

![shixinzhang](http://img.blog.csdn.net/20161129194956317)

基本上都是跟 Function 相关的，这里暂不介绍 1.8 新增的。

从上面内容可知使用自然排序需要类实现 Comparable，并且在内部重写 comparaTo 方法。

而 Comparator 则是在外部制定排序规则，然后作为排序策略参数传递给某些类，比如 Collections.sort(), Arrays.sort(), 或者一些内部有序的集合（比如 SortedSet，SortedMap 等）。

使用方式主要分三步：

1. 创建一个 Comparator 接口的实现类，并赋值给一个对象
 - 在 compare 方法中针对自定义类写排序规则
2. 将 Comparator 对象作为参数传递给 排序类的某个方法
3. 向排序类中添加 compare 方法中使用的自定义类

举个例子： 

			// 1.创建一个实现 Comparator 接口的对象
	        Comparator comparator = new Comparator() {
	            @Override
	            public int compare(Object object1, Object object2) {
	                if (object1 instanceof NewBookBean && object2 instanceof NewBookBean){
	                    NewBookBean newBookBean = (NewBookBean) object1;
	                    NewBookBean newBookBean1 = (NewBookBean) object2;
	                    //具体比较方法参照 自然排序的 compareTo 方法，这里只举个栗子
	                    return newBookBean.getCount() - newBookBean1.getCount();
	                }
	                return 0;
	            }
	        };
	
			//2.将此对象作为形参传递给 TreeSet 的构造器中
	        TreeSet treeSet = new TreeSet(comparator);
	
			//3.向 TreeSet 中添加 步骤 1 中 compare 方法中设计的类的对象
	        treeSet.add(new NewBookBean("A",34));
	        treeSet.add(new NewBookBean("S",1));
	        treeSet.add( new NewBookBean("V",46));
	        treeSet.add( new NewBookBean("Q",26));
	       
	       
其实可以看到，Comparator 的使用是一种策略模式，不熟悉策略模式的同学可以[点这里查看： 策略模式：网络小说的固定套路](http://blog.csdn.net/u011240877/article/details/52346671) 了解。

排序类中持有一个 Comparator 接口的引用：

    Comparator<? super K> comparator;
    
而我们可以传入各种自定义排序规则的 Comparator 实现类，对同样的类制定不同的排序策略。

##总结

Java 中的两种排序方式：

1. Comparable 自然排序。（实体类实现）
2. Comparator 是定制排序。（无法修改实体类时，直接在调用方创建）

**同时存在时采用 Comparator（定制排序）的规则进行比较。**

对于一些普通的数据类型（比如 String, Integer, Double...），它们默认实现了Comparable 接口，实现了 compareTo 方法，我们可以直接使用。

而对于一些自定义类，它们可能在不同情况下需要实现不同的比较策略，我们可以新创建 Comparator 接口，然后使用特定的 Comparator 实现进行比较。

这就是 Comparable 和 Comparator 的区别。