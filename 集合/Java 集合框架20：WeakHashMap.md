WeakHashMap 中的值对象由普通的强引用保持。因此应该小心谨慎，确保值对象不会直接或间接地强引用其自身的键，因为这会阻止键的丢弃。

http://blog.csdn.net/qq_28261343/article/details/52627545