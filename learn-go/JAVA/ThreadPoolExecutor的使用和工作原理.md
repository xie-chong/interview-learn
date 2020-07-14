# <p align="center">ThreadPoolExecutor的使用和工作原理</p>

## 前言

在我们进行开发时，为了加快程序的运行效率，可能会使用到线程池去加快程序效率，但是线程池也不是随便使用的，如果一旦使用错误，还可能会造成生产事故。在JDK1.5后提供了Executor框架来供开发者使用，无需关心任务如何被执行，如果不清楚线程池原理的话，使用Executor框架也可能会造成生产事故，下面主要来分析一下Executor框架和线程池ThreadPoolExecutor底层的原理。



## 1. Executors提供的线程池
    
Executors一共给我们提供了四种类型的线程池，分别是：newSingleThreadExecutor()，newFixedThreadExecutor()，newCachedThreadPool()，newScheduledThreadPool()

### (1) newSingleThreadPool()

创建单一线程的线程池，也就是线程池中只有一个线程在执行

### (2) newFixedThreadPool()

创建一个定长的线程池，也就是线程池中的线程数量是固定大小的

### (3) newCachedThreadPool()

创建一个可缓存的线程池，如果线程池中的线程执行完后，没有Task任务进来，那么当前线程可以缓存60s，如果60s过后，线程没有被调用，则移除该线程，适用于短期异步任务的场景。

### (4) newScheduledThreadPool()

创建一个支持定时和周期性执行的线程池，基于DelayedWorkQueue延迟队列来实现定时执行


## 2. 四种线程池底层实现原理

我们将代码跟入深一点，就会发现四种线程池最终调用的还是 new ThreadPoolExecutor()，只不过传入参数不同而已。在《阿里巴巴开发手册》中明确指出不建议我们直接Executors创建现成线程池，我们可以看到上面的四种线程池它们有一个参数也就是要传入的阻塞队列。

**newSingleThreadPool()** 传入的阻塞队列 **LinkedBlockingQueue()** 基于链表实现的阻塞队列，队列最大长度为Integer.MAX_VALUE，也就是这个队列可以承载2^31大小的线程，这时候服务器肯定会承受不住这么多的线程，会造成服务器崩溃的场景。

**newFixedThreadPool()** 跟 **newSingleThreadPool()** 一样，传入的都是相当于无界的一个队列。

**newCachedThreadPool()**和**newScheduledThreadPool()** 可以看到传入的一个最大线程数为 Integer.MAX_VALUE，也就是会一直创建新的线程去执行任务，这时候服务器也是承受不住的，会造成服务器崩溃。

上述原因也就是阿里开发手册为什么不建议我们直接使用创建好的线程池使用，如果开发者稍有一不注意，就会造成服务器线程崩溃。所以我们应该使用底层的ThreadPoolExecutor()类创建线程池，这样自己能够清楚的知道自己设置线程池和队列大小。

## 3. ThreadPoolExecutor 7个参数

先看一些ThreadPoolExecutor的构造方法：

### (1) corePoolSize

 线程池中存在的核心线程数，也就是线程池中不会被释放的线程数量，有网友可能会有疑问，如果线程不会被释放，那不会一直被占着资源？不会，存活的线程不会跟其它线程抢占资源，相当于处于一个假死状态，有任务过来就会唤醒存活的线程执行任务

###  (2) maximumPoolSize

线程池中允许的最大线程数，也就是任务数 > corePoolSize并且阻塞队列也已经放满了任务，这是最大线程数就会起作用，则创建新的线程执行任务，前提是任务数 < maximumPoolSize

### (3) keepAliveTime

线程空闲时的存活时间，大于corePoolSize数量被创建的线程在没有任务执行的情况下的存活时间，一旦达到存活时间线程则会被回收释放

### (4) unit

keepAliveTime存活时间的单位

### (5) workQueue

当任务数 > corePoolSize核心线程数时，workQueue就会起作用，用来保存等待被执行的任务的阻塞队列，JDK提供了多种阻塞队列：

1. ArrayBlockingQueue：基于数组结构的有界阻塞队列，按FIFO排序任务

2. LinkedBlockingQuene：基于链表结构的阻塞队列，按FIFO排序任务

3. SynchronousQuene：一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态

4. priorityBlockingQuene：具有优先级的无界阻塞队列

5. DelayQueue: 支持延时获取元素的无界阻塞队列，在创建元素时可以指定多久才能从队列中获取当前元素

6. LinkedTransferQueue: 由一个链表结构组成的无界阻塞TransferQueue队列

7. LinkedBlockingDeque: 由一个链表结构组成的双向阻塞队列，可以从队列的两端插入和移出元素

### (6) threadFactory

创建线程的工厂，默认实现DefaultThreadFactory

### (7) handler

线程池的淘汰策略，当过来的任务数 > corePoolSize, 阻塞队列已满，> maximumPoolSize, 这时handler就会起作用，会采用一种策略来处理过来的任务数，JDK也提供了4中淘汰策略：

1. AbortPolicy：直接抛出异常，默认策略
2. CallerRunsPolicy：用调用者所在的线程来执行任务
3. DiscardOldestPolicy: 丢弃队列中最近的一个任务，并执行当前任务
4. DiscardPolicy: 不处理，直接丢弃掉

当然我们也可以自定义自己的淘汰策略，需要实现接口RejectedExecutionHandler，重写rejectedExecution方法，可以自定义加一些日志或者持久化不能处理的任务

## 4. ThreadPoolExecutor工作流程

当一个新的任务提交给线程池时，线程池的处理步骤：

(1) 首先判断核心线程数是否已满，如果没满，则调用一个线程处理Task任务，如果已满，则执行步骤(2)

(2) 这时会判断阻塞队列是否已满，如果阻塞队列没满，就将Task任务加入到阻塞队列中等待执行，如果阻塞队列已满，则执行步骤(3)

(3) 判断是否大于最大线程数，如果小于最大线程数，则创建线程执行Task任务，如果大于最大线程数，则执行步骤(4)

(4) 这时会使用淘汰策略来处理无法执行的Task任务

大概的工作流程：

为了更好的理解，可以将线程池比喻成一个工厂，核心线程数就好比是工厂里面正式员工，工厂可能接受到了很多订单要进行生产，这时人手不够，如果加人手，这时从外面招了几个临时员工，但是也不可能一直招，会有个最大的限制，这时就是最大线程数了，这部分临时员工就属于 > 阻塞队列，< 最大线程数中的线程。阻塞队列就好比工厂里面的仓库，正式员工不够，后面过来的订单生产就会被放到仓库中。

## 5. 线程的状态详解

线程的状态相信大家可能都知道，但有时容易忘记，记不住各种状态的转换，在JDK中Thread类中提供了一个枚举类，这个枚举类就例举了线程的各个状态

我们可以看到一共定义了6个枚举值，其实代表的是5种类型的线程状态, 5种线程状态：

### 1.NEW(新建)

### 2.RUNNABLE(运行状态)

### 3.BLOCKED(阻塞状态)

### 4.WAITING(等待状态，WAITING和TIMED_WAITING可以归为一类，都属于等待状态，只是后者可以设置等待时间，即等待多久)

### 5.TERMINATED(终止状态)

线程关系转换图：

当new Thread()说明这个线程处于NEW(新建状态)，调用Thread.start()方法表示这个线程处于RUNNABLE(运行状态)，但是RUNNABLE状态中又包含了两种状态，一个是READY(就绪状态), RUNNING(运行中)，我们都知道调用start()线程不一定获得了CPU时间片，这时候就处于READY，等待CPU时间片，当获得了CPU时间片，这时就会处于RUNNING状态，可能在运行中调用synchronized同步的代码块，没有获取到锁，这时会处于BLOCKED(阻塞状态)，当重新获取到锁时，又会变为RUNNING状态，中间在代码执行的过程中可能会碰到Object.wait()等一些等待方法，线程的状态又会转变为WAITING(等待状态)，等待被唤醒，当调用了Object.notifyAll()唤醒了之后线程执行完就会变为TERMINATED(终止状态)，直至线程的状态转换关系解释到这里。

## 6. 线程池的5种状态

线程池中状态通过2个二进制位（bit）表示，用来表示线程池定义的5个状态：```RUNNING```、```SHUTDOWN```、```STOP```、```TIDYING```和```TERMINATED```。

RUNNING：线程池正常工作的状态，在 RUNNING 状态下线程池接受新的任务并处理任务队列中的任务

SHUTDOWN：调用```shutdown()```方法会进入 SHUTDOWN 状态。在 SHUTDOWN 状态下，线程池不接受新的任务，但是会继续执行任务队列中已有的任务。

STOP：调用```shutdownNow()```会进入 STOP 状态。在 STOP 状态下线程池既不接受新的任务，也不处理已经在队列中的任务。对于还在执行任务的工作线程，线程池会发起中断请求来中断正在执行的任务，同时会清空任务队列中还未被执行的任务

TIDYING：当线程池中的所有执行任务的工作线程都已经终止，并且工作线程集合为空的时候，进入 TIDYING 状态

TERMINATED：当线程池执行完```terminated()```钩子方法以后，线程池进入终态 TERMINATED


## 总结：

以上是对ThreadPoolExecutor()线程池的一个总结，包括每个参数什么意思，线程池的工作流程，线程的状态转换，还有线程池的状态，写的还是比较基础的，没有对照着源码具体去分析线程池的工作状态，有时间的话将ThreadPoolExecutor()工作状态写的更深一点

在工作中，一直认为编程代码不是最重要的，重要的是在工作中所养成的编程思维。
