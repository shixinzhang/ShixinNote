Collections

Collections是一个集合工具类，它没有构造方法，所有的成员方法都是静态的。利用它可以实现对集合的一些特殊操作，如排序、二分查找、取最值、反转等。
1、 对List集合进行排序
Collections.sort(list)，Collections.sort( list, comparator)。
2、 获取集合的最大值
Collections.sort(collection)，Collections.sort(collection，comparator)。
3、 二分查找
binarySearch(List<? extends Comparable<? super T>> list, T key)
binarySearch(List<? extends T> list, T key, Comparator<? super T> c)
4、 填充
fill（list，obj），将集合所有元素都换成obj
5、 替换
replaceAll(List<T> list, T oldVal, T newVal)，将集合中出现的所有的某一个值，替换为另一个。
6、 反转
reverse(List<?> list)，头变尾，尾变头。
7、随机排列
shuffle(list)，应用：洗牌、掷色子。
重点掌握的下边三个方法
1、 reverseOrder：强行逆转比较器的自然顺序或元素的自然顺序。
2、 将集合由线程不安全转为线程安全的
synchronizedCollection(Collection<T>c)
synchronizedList(List<T>list)
synchronizedMap(Map<K,V>m)
synchronizedSet(Set<T>s)
其原理是：内部封装一个容器，然后定义一个锁，使用该锁同步并封装该容器的所有方法。


http://blog.csdn.net/nikobony/article/details/8691120