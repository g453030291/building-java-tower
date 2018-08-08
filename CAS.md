# CAS

核心：**C**ompare**A**nd**S**wapInt或者double或者long、object等等

​	    比较	     和   交换

![CAS.png](https://github.com/g453030291/building-java-tower/blob/master/images/CAS.png)

AtomicInteger实现CAS的核心方法：每次去自增的时候，都去判断取到的当前值是否和底层的值相等，如果不相等，就循环去取，直到相等时，在进行自增计算。（do while结构）

AtomicBoolean:重要方法方法

compareAndSet：标记某短代码，只有一个线程能够访问，访问后，立即将当前状态修改为true/false。保证此段代码只会有一个线程只执行一次。

CAS：

​	**理解：因为主内存和缓存中的值会出现不一致的情况，比如主内存中a=2，A和B线程同时都取到，每个线程在每次操作前，都去主内存当中取出a来判断当前缓存中的和主内存中的是否一致，如果不一致，会不断去取主内存当中的值，直到相等在进行计算，然后刷新回主内存。**

优点：保证了线程安全

缺点：底层是用一个死循环实现的，在线程数较少（竞争不激烈）时不会有太大的性能的影响，但是在竞争激烈的情况下，会大量的消耗性能

AtomicLong和LongAdder：

并发量大使用LongAdder(底层将压力分散，性能会更好)

要保证统计值的准确使用AtomicLong

AtomicIntegerFieldUpdater<TestController>	updater=AtomicIntegerFieldUpdater.newUpdater(类的class,字段名);

AtomicIntegerFieldUpdater<>:使用原子性的更新某个类的某个变量的值(该变量必须用volatile修饰且非static字段)使用compareAndSet方法

AtomicStampedReference解决CAS的ABA问题。（源码中加入了stamp：版本号来解决ABA问题）

# **Synchronized**

对于synchronized关键字来说，将它修饰代码块，或者修饰方法。意义是相同的。

Synchronized和Lock：

Synchronized：不可中断锁，适合竞争不激烈，可读性好

Lock：可中断锁，多样化同步，竞争激烈时能维持常态

Atomic:竞争激烈时能维持常态，比Lock性能好，只能同步一个值

可见性：（JMM关于synchronized的两条规定）

​	线程解锁前，必须把共享变量的最新的值刷新到主内存当中

​	线程加锁时，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值

# **Volatile**

通过加入**内存屏障**和**禁止重排序**优化来实现

每次读写都会强迫线程去主内存中读、写变量的值

使用场景：标记一个boolean值去标记状态

# 线程安全的并发容器

Arraylist->Copyonwriterarraylist:适合读多写少的场景

缺点：拷贝数组，消耗内存（数据多的时候导致youngGC，fullGC）不能用于实时读的场景(只能保证最终一致)

思想：读写分离、最终一致性、使用时另外开辟空间，解决并发冲突的问题

实现原理：

​	读：在原数组上读，不加锁  写：加锁，避免复制出多个数组

​	写：加锁->复制新的数组，加入新元素->将原有数组指向新数组->解锁

Hashset->Copyonwriterarrayset:

底层用Copyonwriterarraylist实现

Treeset->concurrentskiplistset:（不能为null）

底层基于map

Hashmap->concurrenthashmap:（不允许null）

Treemap->concurrentskiplistmap:

使用skiplist（跳表）实现

Key有序

并发性更好(并发性能是concurrenthashmap的4倍)

# AbstractQueuedSynchronizer-AQS

同步组件：countdownlatch：闭锁,通过计数,实现阻塞线程，是一个计数器，用在多线程环境

countDown()（当前计数减一）、await()（挂起当前线程）

Samephore(信号量)：控制同一时间同个资源的并发线程数

Acquire()获取一个许可、release()释放一个许可、tryacquire()尝试获取许可（获取到就执行，获取不到就丢弃请求）

Cylicbarrier：完成多个线程相互等待，用计数器实现

ReentrantLock:(原理:自旋锁，通过循环调用CAS实现加锁，性能好因为避免线程进入内核态阻塞状态)

1.可以指定公平锁、非公平锁（sync只有非公平锁）

2.提供了一个condition类，可以分组唤醒需要唤醒的线程(sync只能随机唤醒一个线程、或者全部唤醒)

3.提供能够终端等待锁的线程机制，lock.lockInterruptibly()

synchronized区别：都是和可重入锁、锁的实现方式不同（JVM层、代码层）、性能的区别（sync优化后性能差不多）、功能区别（手动释放锁、锁的细粒度）

ReentrantreadwriteLock:(悲观锁)

取得对应类没有任何读写锁的情况下获取写锁

stampLock:（版本和模式两部分组成）

三种锁：写、读、乐观读

Condition:

Futuretask:

只有少量竞争者用：sync（JVM会自动解锁）

可预见的竞争者：ReentrantLock



Callable:得到线程返回值，泛型就是返回值类型

Future接口：可得到其他线程执行情况

futureTask：实现了callable和runnable，可以将两类组合使用，将传入的runnable转为callable，并可以取出返回值



# Fork/Join框架：（工作窃取算法）

将一个大任务放到一个双端队列，主线程从头部开始一个一个任务执行，窃取线程从尾部开始窃取任务执行

优点：充分利用线程进行并行计算、减少了线程间的竞争

缺点：消耗了更多的系统资源（创建了更多的线程、更多的双端队列）



# BlockingQueue

(阻塞队列，主要用在生产者和消费者场景)

阻塞：队列满的时候入队、队列空的时候出队，就会发生阻塞

![阻塞队列.png](https://github.com/g453030291/building-java-tower/blob/master/images/阻塞队列.png)

实现：

ArrayBlockingQueue：FIFO

DelayQueue：按照元素过期时间优先级进行排序

LinkedBlockingQueue：FIFO，链表实现

PriorityBlockingQueue：允许插入null，可以定义队列优先级排序

SynchronousQueue：只能有一个元素(只要插入一个元素就会阻塞，只有等待这个元素被取走，才能继续插入)

# 线程池

Executors.newCachedThreadPool();

Executors.newFixedThreadPool();

Executors.newSingleThreadExecutor();单线程的线程池

Executors.newScheduledThreadPool();调度，可指定运行时间，延迟几秒执行，间隔几秒执行

做延迟的类：Timer，可以按照间隔时间执行多线程任务

线程池合理配置：cpu密集型为:cpu数量+1

​				Io密集型：2*cpu数量

# 多线程并发实践

1. 使用本地变量

2. 使用不可变类

3. 最小化锁的作用域范围：S=1/(1-a+a/n)阿姆达尔定律/安达尔定理

4. 使用线程池的Executor，而不用直接new Thread

5. 宁可使用同步也不使用线程的wait和notify(例如使用：countLauch、samphore)

6. 使用BlockingQueue实现-生产-消费模式

7. 使用并发集合而不是加锁的同步集合（使用currentHashMap、copyOnWriterArrayList、copyOnWriterArraySet、BlockingQueue、DelayQueue、BlockingDelayQueue等并发集合，不使用collection.SynchronousArrayList等）

8. 使用Semaphore创建有界访问

9. 宁可使用同步代码块，也不使用同步方法（同步方法会锁定更多的对象）

10. 避免使用静态变量（）



# 高并发处理思路

同步和异步调用：

同步：rest和rpc

异步：消息队列

应用限流-算法：

计数器法（临界问题）、

滑动窗口、漏桶算法、令牌桶算法

数据库分表：

横向分表：根据id或者其他条件分表

纵向分表：根据活跃数据和不活跃数据进行分表



