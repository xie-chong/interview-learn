# <p align="center">Java 生成随机数 Random</p>

在看Java编程思想的时候，看到了下面这句：
```
private Random rand = new Random(47);
```

而平时使用的都是如下的方式来生成随机数
```
Random rand=new Random()；
int i=rand.nextInt(20);
```

1. 代码示例，无论执行多少次，**10个随机数数值、顺序相同**

```
public class RegisteredFactories {
    public static void main(String[] args) {
        int m = 10;
        Random rand = new Random(47);
        while (m > 0) {
            int n = rand.nextInt(10);
            System.out.print(n + " ");
            m--;
        }
    }
} /* Output:
8 5 3 1 1 9 8 0 2 7
*///:~
```

2. 代码示例，无论执行多少次，**10个随机数是一个固定值**

```
public class RegisteredFactories {
    public static void main(String[] args) {
        int m = 10;
        while (m > 0) {
            Random rand = new Random(47);
            int n = rand.nextInt(10);
            System.out.print(n + " ");
            m--;
        }
    }
} /* Output:
8 8 8 8 8 8 8 8 8 8
*///:~
```

3. 代码示例，无论执行多少次，**10个随机数每一次都不同**

```
public class RegisteredFactories {
    public static void main(String[] args) {
        int m = 10;
        while (m > 0) {
            Random rand = new Random();
            int n = rand.nextInt(10);
            System.out.print(n + " ");
            m--;
        }
    }
} /* Output:
第一次执行输出： 4 1 2 4 6 7 2 3 9 5
第一次执行输出： 3 9 3 7 2 8 7 3 5 3
*///:~
```

**所以可以这么理解理解这个“47”：Random的含参构造器里的参数是一种随机数生成规则，此后Random对象都依照这个规则生成随机数，故当然这个每次运行的结果都一致。当然这个“47”是可以改变数值的。**

4. 源码（部分）：

jdk1.8.0_231\jre\lib\rt.jar!\java\util\Random.class
```
public class Random implements java.io.Serializable {

    // ......

    /**
     * Creates a new random number generator. This constructor sets
     * the seed of the random number generator to a value very likely
     * to be distinct from any other invocation of this constructor.
     */
    public Random() {
        this(seedUniquifier() ^ System.nanoTime());
    }
	
    private static long seedUniquifier() {
        // L'Ecuyer, "Tables of Linear Congruential Generators of
        // Different Sizes and Good Lattice Structure", 1999
        for (;;) {
            long current = seedUniquifier.get();
            long next = current * 181783497276652981L;
            if (seedUniquifier.compareAndSet(current, next))
                return next;
        }
    }
	
	    private static final AtomicLong seedUniquifier
        = new AtomicLong(8682522807148012L);

    /**
     * Creates a new random number generator using a single {@code long} seed.
     * The seed is the initial value of the internal state of the pseudorandom
     * number generator which is maintained by method {@link #next}.
     *
     * <p>The invocation {@code new Random(seed)} is equivalent to:
     *  <pre> {@code
     * Random rnd = new Random();
     * rnd.setSeed(seed);}</pre>
     *
     * @param seed the initial seed
     * @see   #setSeed(long)
     */
    public Random(long seed) {
        if (getClass() == Random.class)
            this.seed = new AtomicLong(initialScramble(seed));
        else {
            // subclass might have overriden setSeed
            this.seed = new AtomicLong();
            setSeed(seed);
        }
    }
	
	    private static long initialScramble(long seed) {
        return (seed ^ multiplier) & mask;
    }

    /**
     * Sets the seed of this random number generator using a single
     * {@code long} seed. The general contract of {@code setSeed} is
     * that it alters the state of this random number generator object
     * so as to be in exactly the same state as if it had just been
     * created with the argument {@code seed} as a seed. The method
     * {@code setSeed} is implemented by class {@code Random} by
     * atomically updating the seed to
     *  <pre>{@code (seed ^ 0x5DEECE66DL) & ((1L << 48) - 1)}</pre>
     * and clearing the {@code haveNextNextGaussian} flag used by {@link
     * #nextGaussian}.
     *
     * <p>The implementation of {@code setSeed} by class {@code Random}
     * happens to use only 48 bits of the given seed. In general, however,
     * an overriding method may use all 64 bits of the {@code long}
     * argument as a seed value.
     *
     * @param seed the initial seed
     */
    synchronized public void setSeed(long seed) {
        this.seed.set(initialScramble(seed));
        haveNextNextGaussian = false;
    }
	
	// ......
	
}

```
