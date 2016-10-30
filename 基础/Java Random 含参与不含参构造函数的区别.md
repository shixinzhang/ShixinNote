##Random 通常用来作为随机数生成器，它有两个构造方法：


            Random random = new Random();
            Random random2 = new Random(50);

1.不含参构造方法：

    public Random() {
        setSeed(System.nanoTime() + seedBase);
        ++seedBase;
    }

2.含参构造方法：

    public Random(long seed) {
        setSeed(seed);
    }

都调用的 setSeed 方法：

    public synchronized void setSeed(long seed) {
        this.seed = (seed ^ multiplier) & ((1L << 48) - 1);
        haveNextNextGaussian = false;
    }

###可以看到，不含参构造方法每次都使用当前时间作为种子，而含参构造方法是以一个固定值作为种子

##什么是种子 seed 呢？

seed 是 Random 生成随机数时使用的参数：

Random 中最重要的就是 next(int) 方法，使用 seed 进行计算:

    protected synchronized int next(int bits) {
        seed = (seed * multiplier + 0xbL) & ((1L << 48) - 1);
        return (int) (seed >>> (48 - bits));
    }

其他 nextXXX 方法都是调用的 next()。

比如 nextInt(int):

    public int nextInt(int n) {
        if (n <= 0) {
            throw new IllegalArgumentException("n <= 0: " + n);
        }
        if ((n & -n) == n) {
			//调用 next()
            return (int) ((n * (long) next(31)) >> 31);
        }
        int bits, val;
        do {
            bits = next(31);
            val = bits % n;
        } while (bits - val + (n - 1) < 0);
        return val;
    }

再比如 nextBoolean():

	//也是调用的 next()
    public boolean nextBoolean() {
        return next(1) != 0;
    }

##举个栗子：

    @Test
    public void testRandomParameter(){
        System.out.println("Random 不含参构造方法：");
        for (int i = 0; i < 5; i++) {
            Random random = new Random();
            for (int j = 0; j < 8; j++) {
                System.out.print(" " + random.nextInt(100) + ", ");
            }

            System.out.println("");
        }

        System.out.println("");

        System.out.println("Random 含参构造方法：");
        for (int i = 0; i < 5; i++) {
            Random random = new Random(50);
            for (int j = 0; j < 8; j++) {
                System.out.print(" " + random.nextInt(100) + ", ");
            }
            System.out.println("");
        }
    }

分别用含参构造方法和不含参构造方法创建 5 个随机生成器对象，每个随机生成器再生产 8 个随机数，对比下结果：

![这里写图片描述](http://img.blog.csdn.net/20161030105332800)

再运行一次：

![这里写图片描述](http://img.blog.csdn.net/20161030105425114)




##总结：

通过上述例子可以发现：

**随机数是种子经过计算生成的**。

- 不含参的构造函数每次都使用当前时间作为种子，随机性更强
- 而含参的构造函数其实是伪随机，更有可预见性