5个方向：atomic（CAS）原子类、locks锁、executor线程池、collections线程安全集合、tools（AQS）并发安全类

#### Atomic：

CompareAndSwapInt(CAS):拿当前对象的值，与底层的值进行对比，相同才进行操作。不相同就循环取值，直到相同再进行操作。

AtomicIntegerFieldUpdate：原子性的更新某个变量的属性

AtomicStampReference（解决CAS的ABA问题）

AtomicBoolean：（compareAndSet方法）实现某段代码只执行一次

原子性的对比：

synchronized：不可中断锁、适合竞争不激烈、可读性好(竞争激烈时性能下降比较厉害)

Lock：可中断锁、多样化同步、竞争激烈时能维持常态

Atomic：竞争激烈时能维持常态、比Lock性能好、只能同步一个值

可见性：

synchronized：1.解锁前必须把共享变量最新值刷新到主内存

​    2.线程加锁时，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值（加锁与解锁时是同一把锁）

volatile：（使用场景：1.作为多个线程间的状态标示量2.double-check）

原理：1.加入内存屏障(1)写操作时，在写操作后加入一条store屏障指令，将本地内存中的值刷新到主内存；(2)读操作前加入load屏障指令，从主内存中读取共享变量

   2.禁止重排序优化

#### 线程不安全的单例：（懒汉式）

public class Singleton {

​    private Singleton(){

​    }

​		volatile+double-check机制 -> 禁止指令重排

​    private <u>②volatile</u> static Singleton instance = null;



​    public static <u>①synchronized</u> Singleton getInstance(){

​        if(instance==null){

​	<u>	②synchronized (Singleton.class){//同步锁  double-check机制</u>

​            	  instance = new Singleton();

   }

​        }

​        return instance;

​    }

}

#### 线程安全的单例：（饿汉式）

public class Singleton {

​    private Singleton(){

​    }

​    private static Singleton instance =  new Singleton();

​    public static Singleton getInstance(){

​        return instance;

​    }

}

#### ThreadLocal：实现线程封闭

内部维护了一个map，key是线程名，value是要封闭的对象

#### Collections：线程安全的同步容器

ArrayList->Vector、Stack

HashMap->HashTable（K、V不能为null）

Collections.synchronizedXXX(List、Set、Map)

#### 并发容器：

Arraylist->Copyonwriterarraylist:适合读多写少的场景

缺点：拷贝数组，消耗内存（数据多的时候导致youngGC，fullGC）

不能用于实时读的场景(只能保证最终一致)

思想：读写分离、最终一致性、使用时另外开辟空间，解决并发冲突的问题

实现原理：

读：在原数组上读，不加锁  写：加锁，避免复制出多个数组

写：加锁->复制新的数组，加入新元素->将原有数组指向新数组->解锁

Hashset->Copyonwriterarrayset:

底层用Copyonwriterarraylist实现

Treeset->concurrentskiplistset:（不能为null）

底层基于map

Hashmap->concurrenthashmap:（不允许null）

 

Treemap->concurrentskiplistmap:

使用skiplist（跳表）实现

Key有序

并发性更好(并发性能是concurrenthashmap的4倍)

#### AQS:实现两个功能1.排它锁（实现独占控制）2.共享锁（实现共享控制）

底层使用的双向链表，使用Node实现FIFO队列，可以用于构建锁，利用了一个int型成员变量state来表示状态

实现原理：内部维护了一个CLH队列，来管理锁，线程会首先尝试获取锁，如果失败，就将当前线程以及等待状态等信息包装为一个node节点，加入到同步队列syncQueue里，然后会不断地尝试循环获取锁，条件是当前节点为head的直接后继才会尝试，如果失败，会阻塞自己，直到自己被唤醒，当持有锁的线程释放锁的时候，会唤醒队列中的后继线程。

#### CountDownLatch：

原理：内部维护一个计数器，每次调用countDown方法，计数器为0时，调用await方法等待的线程会被唤醒

使用场景：，可以完成阻塞一个或多个线程的功能，程序执行需要等待某个操作先执行结束之后在执行

常用方法：countDown()；计数器减1

   		await()；继续执行下面的线程

#### Semaphore：信号量

原理：内部维护一个有限大小的链表

使用场景：并发访问控制（如数据库连接数），控制某个资源被同时访问的个数

常用方法：acquire(N)；获取N个许可

   			releace(N)；释放N个许可

 			 tryAcquire()；尝试获取许可，没获取到的就放弃

#### CyclicBarrier：

原理：计数器

使用场景：多个线程相互等待，直到都到达某个状态再继续执行（用于多线程计算数据，最后合并计算结果）

常用方法：await()；标记当前线程状态OK

CyclicBarrier和CountDownLatch的区别：

1.CyclicBarrier计数器可重置，CountDownLatch只能使用一次

2.CountDownLatch描述的是一个或者多个线程等待其他线程的关系，CyclicBarrier描述的是多个线程内部相互等待的关系

#### ReenttantLock：

原理:自旋锁，通过循环调用CAS实现加锁，性能好因为避免线程进入内核态阻塞状态

使用场景：

#### ReenttantLock和synchronized的区别：

1.二者都是可重入锁

2.sync依赖于JVM实现，ReenttantLock依赖于JDK实现

3.sync优化后性能差不多

4.①sync更方便，编译器保证加锁释放，ReenttantLock必须手动释放锁

   ②锁的细粒度和灵活度ReenttantLock更强大

5.ReenttantLock可以指定使用公平锁还是非公平锁（公平锁就是先等待的线程先获得锁）sych只能是非公平锁

6.ReenttantLock可使用一个Condition类，用来分组唤醒需要唤醒的线程；sych只可以随机唤醒一个线程或者全部唤醒所有线程

7.ReenttantLock提供了一个能够中断等待锁线程的机制，lock.lockInterruptibly()；

#### FutureTask：

Callable<V>接口返回值类型就是创建Callable时的V

Future接口，可以监视目标线程的执行情况，调用Future的get方法，可以得到返回值。

FutureTask的父类->RunnableFuture继承->Runnable、Future接口

#### Fork/Join框架：

作用：把一个大任务，分为若干个小任务，然后在合并结果。

实现逻辑：工作窃取算法

#### BlockingQueue阻塞队列：线程安全

特点：队列为空时，消费队列被阻塞

使用：生产者/消费者场景

实现：ArrayBlockingQueue：FIFO

   		DelayQueue：排序，

   		LinkedBlockingQueue：无边界（maxInteger），链表，FIFO

   		PrioityBlockingQueue：无边界，允许null，必须实现copareable接口

   		SynchronousQueue：只允许有一个元素，同步队列，无界队列

#### 线程池：

ThreadPoolExecutor：

参数详解①corePoolSize：核心线程数量

②maximumPoolSize：线程最大线程数

③workQueue：阻塞队列，存储等待执行的任务，很重要，会对线程池的运行过程产生重大影响

④keepAliveTime：线程没有执行时最多保持多久时间终止

⑤unit：keepAliveTime的时间单位

⑥threadFactory：线程工厂，用来创建线程

⑦rejectHandler：当拒绝处理任务时的策略

重要方法：

execute（）：提交任务，交给线程池执行

submit（）：提交任务，能够返回执行结果（相当于execute+Future）

shutDown（）：关闭线程池，等待任务都执行完

shutDownNow（）：关闭线程池，不等待任务执行完

getTaskCount（）：线程池已执行和未执行的任务总数

getCompletedTaskCount（）：已完成的任务数量

getPoolSize（）：线程池当前的线程数量

getActiveCount（）：当前线程池中正在执行任务的线程数量

常用线程池：

Executors.newCachedThreadPool：创建一个可缓存的线程池，如果线程池的长度超过了需要的长度可以灵活回收空闲线程，如果没有可以回收的，就新建线程

Executors.newFixedThreadPool：创建一个定长的线程池，可以控制线程的最大并发数，超出的线程会进入队列等待

Executors.newScheduledThreadPool：创建一个定长的线程池，支持定时、周期性的任务执行

Executors.newSingleThreadExecutor：单线程化的线程池，会使用唯一一个工作线程执行任务，保证所有任务按照指定顺序，顺序执行，可以指定是按照FIFO，或者优先级等顺序执行

#### HashMap与ConcurrentHashMap：

HashMap有两个参数影响它的性能：①初始容量16②加载因子0.75