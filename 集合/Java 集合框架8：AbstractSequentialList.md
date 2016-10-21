###[点击查看 Java 集合框架深入理解 系列](http://blog.csdn.net/u011240877/article/category/6447444)， - ( ゜- ゜)つロ 乾杯~ 
------------------


####AbstractSequentialList 没有什么特别的，这里介绍是为了理解 LinkedList 更容易。

![这里写图片描述](http://img.blog.csdn.net/20161018235753095)
##什么是 AbstractSequentialList

（ Sequential 相继的，按次序的）

AbstractSequentialList 继承自 [AbstractList](http://blog.csdn.net/u011240877/article/details/52834074)，是 `LinkedList` 的父类，是 [List 接口](http://blog.csdn.net/u011240877/article/details/52802849) 的简化版实现。

简化在哪儿呢？简化在 AbstractSequentialList **只支持按次序访问**，而不像 [AbstractList](http://blog.csdn.net/u011240877/article/details/52834074) 那样支持随机访问。

想要实现一个支持按次序访问的 List的话，只需要继承这个抽象类，然后把指定的抽象方法实现就好了。需要实现的方法：

- size()
- listIterator()，返回一个 ListIterator

你需要实现一个 `ListIterator`, 实现它的 `hasNext()`, `hasPrevious()`, `next()`, `previous()`, 还有那几个 **获取位置** 的方法，这样你就得到一个不可变的 ListIterator 了。如果你想让它可修改，还需要实现 `add()`, `remove()`, `set()` 方法。

正如在 每个 [Collection 接口](http://blog.csdn.net/u011240877/article/details/52773577) 中提倡的那样，AbstractSequentialList 的子类需要提供两个构造函数，一个无参，一个以 Collection 为参数。


##成员函数
AbstractSequentialList 在 AbstractList 的基础上实现了以下方法：

![这里写图片描述](http://img.blog.csdn.net/20161018235652391)

###1.获取迭代器：

    public Iterator<E> iterator() {
		//调用继承自
        return listIterator();
    }
	
	//继承 AbstractList 的 listIterator()
    public ListIterator<E> listIterator() {
        return listIterator(0);
    }

	//需要实现类实现的方法
    public abstract ListIterator<E> listIterator(int index);

###2.add(int, E) 添加元素到指定位置，将当前处于该位置（如果有的话）和任何后续元素的元素移到右边（添加一个到它们的索引）：

    public void add(int index, E element) {
        try {
			//调用 ListIterator.add()
            listIterator(index).add(element);
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

####如果 Listerator 的实现类实现 add() 方法，会报 `UnsupportedOperationException` 错。

###3.addAll(int index, Collection<? extends E> c) 添加一个集合的所有元素到指定位置： 

    public boolean addAll(int index, Collection<? extends E> c) {
        try {
            boolean modified = false;
            ListIterator<E> e1 = listIterator(index);
            Iterator<? extends E> e2 = c.iterator();
			//迭代遍历 指定集合 c 
            while (e2.hasNext()) {
				//用 获取到的 listIterator 逐个添加
                e1.add(e2.next());
                modified = true;
            }
            return modified;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

####用获取到的 listIterator 逐个添加集合中的元素,这就要考验 ListIterator.add 方法的实现效率了，总不能每次都后移一位吧

####的确在目前集合框架中 AbstractSequentialList 的唯一实现类 LinkedList 实现的 ListIterator 中，由于 LinkedList 的双休链表特性，每次 add 只需要调整指针指向就可以了。

###4.get(int index) 获取指定位置的元素：

    public E get(int index) {
        try {
            return listIterator(index).next();
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

###5.set(int index, E element) 修改指定位置的元素为新的：

    public E set(int index, E element) {
        try {
            ListIterator<E> e = listIterator(index);
            E oldVal = e.next();
            e.set(element);
            return oldVal;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

###6.remove(int index) 删除指定位置的元素：

    public E remove(int index) {
        try {
            ListIterator<E> e = listIterator(index);
            E outCast = e.next();
            e.remove();
            return outCast;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

##总结


####可以看到， AbstractSequentialList 把父类 [AbstractList](http://blog.csdn.net/u011240877/article/details/52834074) 中没有实现或者没有支持的操作都实现了，而且都是**调用的 [ListIterator](http://blog.csdn.net/u011240877/article/details/52834074) 相关方法**进行操作。

####在 [ Java 集合深入理解：AbstractList](http://blog.csdn.net/u011240877/article/details/52834074) 中我们介绍了 RandomAccess，里面提到，支持 RandomAccess 的对象，遍历时使用 get 比 迭代器更快。


####而 AbstractSequentialList 只支持迭代器按顺序 访问，不支持 RandomAccess，所以遍历 AbstractSequentialList 的子类，所以时 get 的效率要 <= 迭代器。


	int i=0, n=list.size(); i &lt; n; i++)
          list.get(i);

get()太慢，还不如用迭代器：

  	for (Iterator i=list.iterator(); i.hasNext(); )
      	 i.next();

##Thanks

http://www.trinea.cn/android/arraylist-linkedlist-loop-performance/



