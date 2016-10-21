先考两道题：

    Integer a1 = 300;
    Integer a2 =300;
    System.out.print(a1 == a2);

    Integer b1 = 1;
    Integer b2 = 1;
    System.out.print(a1 == a2);

答案是：
	
	false

	true


Integer a1=1的时候，java编译器会默认编译成Integer a1=Integer.valueOf(1),valueOf方法里边会查看a1的值是否在-128到127，如果在这个范围，会从IntegerCache这个缓存中去取有没有值等于1的对象，如果有的话会返回缓存里的对象，没有的话会new一个。

马丹，那人说错了
integer 在-128 到127之间是静态初始化的
直接从缓存里面取得

JDK 1.7:

![这里写图片描述](http://img.blog.csdn.net/20161021102654195)

JDK 1.8:
![这里写图片描述](http://img.blog.csdn.net/20161021102946743)

IntegerCache:

    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }


自动装箱的缓存？？
	
Long　也是：

![这里写图片描述](http://img.blog.csdn.net/20161021103147572)

![这里写图片描述](http://img.blog.csdn.net/20161021103237298)

##Double，Float 不一样：

Double:
![这里写图片描述](http://img.blog.csdn.net/20161021103350503)

Float:
![这里写图片描述](http://img.blog.csdn.net/20161021103429276)

String??