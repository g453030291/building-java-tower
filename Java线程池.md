#### Java线程池(java.util.concurrent.ThreadPoolExecutor）

public ThreadPoolExecutor(int corePoolSize,

​                              int maximumPoolSize,

​                              long keepAliveTime,

​                              TimeUnit unit,

​                              BlockingQueue<Runnable> workQueue,

​                              ThreadFactory threadFactory,

​                              RejectedExecutionHandler handler)

ThreadPoolExecutor是线程池的核心类，其中有四个构造方法，其他三个构造方法都是调用上面这个7个参数的构造方法。

#### 参数释义：

1.corePoolSize：核心池的大小，这个参数跟后面讲述的线程池的实现原理有非常大的关系。在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；

2.maximumPoolSize：线程池最大线程数，这个参数也是一个非常重要的参数，它表示在线程池中最多能创建多少个线程

3.keepAliveTime：表示线程没有任务执行时最多保持多久时间会终止。默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize，即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0

4.unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性

(TimeUnit是concurrent包中的一个枚举类：TimeUnit.DAYS; //天TimeUnit.HOURS; //小时TimeUnit.MINUTES;  //分钟TimeUnit.SECONDS;  //秒TimeUnit.MILLISECONDS; //毫秒 TimeUnit.MICROSECONDS;  //微妙TimeUnit.NANOSECONDS; //纳秒)

5.workQueue：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列主要有以下几种选择:ArrayBlockingQueue,LinkedBlockingQueue,SynchronousQueue

6.threadFactory：线程工厂，主要用来创建线程

7.handler：表示当拒绝处理任务时的策略，有以下四种取值：

ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 

ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 

ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）

ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务 

#### 重要方法:

excute(),submit(),shutdown(),shutdownNow()

execute()方法实际上是Executor中声明的方法，在ThreadPoolExecutor进行了具体的实现，这个方法是ThreadPoolExecutor的核心方法，通过这个方法可以向线程池提交一个任务，交由线程池去执行。

submit()方法是在ExecutorService中声明的方法，在AbstractExecutorService就已经有了具体的实现，在ThreadPoolExecutor中并没有对其进行重写，这个方法也是用来向线程池提交任务的，但是它和execute()方法不同，它能够返回任务执行的结果，去看submit()方法的实现，会发现它实际上还是调用的execute()方法，只不过它利用了Future来获取任务执行结果（Future相关内容将在下一篇讲述）。

　shutdown()和shutdownNow()是用来关闭线程池的。

#### 任务提交给线程池之后的处理策略:

如果当前线程池中的线程数目小于corePoolSize，则每来一个任务，就会创建一个线程去执行这个任务；

如果当前线程池中的线程数目>=corePoolSize，则每来一个任务，会尝试将其添加到任务缓存队列当中，若添加成功，则该任务会等待空闲线程将其取出去执行；若添加失败（一般来说是任务缓存队列已满），则会尝试创建新的线程去执行这个任务；

如果当前线程池中的线程数目达到maximumPoolSize，则会采取任务拒绝策略进行处理；

如果线程池中的线程数量大于 corePoolSize时，如果某线程空闲时间超过keepAliveTime，线程将被终止，直至线程池中的线程数目不大于corePoolSize；如果允许为核心池中的线程设置存活时间，那么核心池中的线程空闲时间超过keepAliveTime，线程也会被终止。

#### 任务缓存队列及排队策略:

在前面我们多次提到了任务缓存队列，即workQueue，它用来存放等待执行的任务。

workQueue的类型为BlockingQueue<Runnable>，通常可以取下面三种类型：

1）ArrayBlockingQueue：基于数组的先进先出队列，此队列创建时必须指定大小；

2）LinkedBlockingQueue：基于链表的先进先出队列，如果创建时没有指定此队列大小，则默认为Integer.MAX_VALUE；

3）synchronousQueue：这个队列比较特殊，它不会保存提交的任务，而是将直接新建一个线程来执行新来的任务。

#### 任务拒绝策略:

ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 	ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 	ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程） 	ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

#### JDK推荐:

Executors.newCachedThreadPool();        //创建一个缓冲池，缓冲池容量大小为Integer.MAX_VALUE

Executors.newSingleThreadExecutor();   //创建容量为1的缓冲池

Executors.newFixedThreadPool(int);    //创建固定容量大小的缓冲池

