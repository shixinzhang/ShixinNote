为什么删除Set中元素要求这个类要继承Comparable接口，难道实现equals不就行了吗？
举个例子吧
src

Collection<Name> c = new HashSet<Name>();
c.add(new Name("f1","l1"));
boolean isSuccess = c.remove(new Name("f1", "l1")); //？
为什么要实现compareTo方法，java API为什么这样实现？

为什么和hash相关的集合，为什么要实现hashcode方法？
还是上面的例子Name不仅要实现compareTo方法，还要实现hashCode方法啊， 就是为了快速查找吗？