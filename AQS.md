## AbstractQueuedSynchronizer-AQS

是J.U.C的核心类，基于FIFO的队列来实现的，使用继承的方式来使用，可以实现两种功能。独占功能、共享功能。

#### CountDownLatch

两个核心方法countDown（）和await（），只有等到countDown()计数器中的数值减到0的时候，才执行await（）方法后面的代码。

#### Semaphore

Semaphore semaphore = new Semaphore(N);//同时允许N个线程获取资源

两个重要方法acquire（N）获取N个许可

​			release（N）释放N个许可

#### CyclicBarrier

CyclicBarrier cyclicBarrier = new CyclicBarrier（N）；//可以同时有N个线程去互相等待

cyclicBarrier.await（）；//N个线程同时都到达某种状态后，会去同时唤醒，执行下面的操作

CyclicBarrier和countDownLatch的区别：

①：CountDownLatch的计数器只能使用一次，CyclicBarrier可以通过reset方法重置循环使用；

②：CountDownLatch实现一个或者N个线程需要等待其他线程，才能继续往下执行。CyclicBarrier实现了多个线程相互等待。