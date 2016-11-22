PriorityBlockingQueue

接口隔离原则（英语：interface-segregation principles， 缩写：ISP）指明没有客户(client)应该被迫依赖于它不使用方法。接口隔离原则(ISP)拆分非常庞大臃肿的接口成为更小的和更具体的接口，这样客户将会只需要知道他们感兴趣的方法。这种缩小的接口也被称为角色接口（role interfaces）。接口隔离原则(ISP)的目的是系统解开耦合，从而容易重构，更改和重新部署。接口隔离原则是在SOLID (面向对象设计)中五个面向对象设计(OOD)的原则之一，类似于在GRASP (面向对象设计)中的高内聚性。

 
 下面是看到网上的举的例子：
 >可能描述起来不是很好理解，我们还是以示例来加强理解吧。 我们知道，在网络框架中，网络队列中是会对请求进行排序的。内部使用 PriorityBlockingQueue 来维护网络请求队列，PriorityBlockingQueue 需要调用 Request 类的排序方法就可以了，其他的接口他根本不需要，即 PriorityBlockingQueue 只需要 compareTo 这个接口，而这个 compareTo 方法就是我们所说的最小接口方法，而是 Java 中的 Comparable 接口，但我们这里是指为了学习，至于哪里定义的无关紧要。
 +
 
 >在元素排序时，PriorityBlockingQueue 只需要知道元素是个 Comparable 对象即可，不需要知道这个对象是不是 Request 类以及这个类的其他接口。它只需要排序，因此，只要知道它是实现了 Comparable 对象即可，Comparable 就是它的最小接口，也是通过 Comparable 隔离了 PriorityBlockingQueue 类对 Request 类的其他方法的可见性。