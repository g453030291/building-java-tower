#### Java特点

- 面向对象
- 良好的跨平台特性(JVM)
- Java具有垃圾回收机制（Garbage Collection）
- Java为单根结构，Java所有类都继承自Object
- Java在设计初就非常注重安全性，如：不支持指针，避免了非法内存操作，编译运行时，提供了多重语法、类型、边界和字节码的检查
- Java是解释型的语言。Java代码会被编译成class被JVM加载执行。而不是直接运行在操作系统上

#### JVM概述

##### 一、Java内存区域

![Java内存区域.png](https://github.com/g453030291/building-java-tower/blob/master/images/Java内存区域.png)

线程私有：虚拟机栈、本地方栈、程序计数器

线程共享：堆、方法区

程序计数器：指示当前线程所执行的字节码的行号，字节码解释器会通过更改计数器的值来选取下一条需要执行的字节码指令，每个线程的程序计数器都是独立的，来确保各线程间的计数器互不影响；

虚拟机栈：生命周期和线程相同，每个方法执行时会创建一个栈帧、当前执行方法的局部变量表、操作数、动态链接、方法出口等信息。方法的调用和返回对应着栈帧的出栈和入栈的操作；

本地方法栈：调用native方法；

方法区：存储已经被虚拟机加载的类的信息、常量和静态变量等数据；

堆：存储对象的实例，垃圾回收的主要区域；

##### 二、Java内存模型

![Java内存模型.png](https://github.com/g453030291/building-java-tower/blob/master/images/Java内存模型.png)

##### 三、垃圾回收

判断哪些对象需要回收：

​	可达性分析，根据GC Roots搜索引用链，当一个对象没有GC Roots引用链相连，就证明该对象是不可用的。在进行可达性分析时，需要让整个系统冻结在某个时间点，对外表现为所有工作进程都停止，如此才能准确的获取到GC Roots，这个过程称为stop the world。

回收算法：

①：标记-清除算法：首先标记出需要回收的对象，然后统一回收

②：复制算法：①中的内存碎片过多，复制算法将内存按照容量划分为大小相同的两块，每次只使用其中一块，一块使用完，将存活的对象复制到另一块中，然后将使用的那块空间一次性整理

③：标记-整理算法：@中内存利用率不高，过程和方法①类似，首先对对象进行标记，然后将仍存活的对象向一端移动。然后清理边界以外的内存区域。

④：分代收集算法：将堆分为新生代和老年代。根据对象的生命周期，分别放入不同的内存区域中，同时针对不同垃圾回收特点的对象采用不同的回收策略。**新生代分为一块较大的Eden区和两个较小的survival区，因为新生代大部分对象都需要回收，所以采用复制算法进行回收。而老年代中需要回收的对象较少，因此采用标记-清除或者标记-整理算法进行回收。基于上述垃圾回收算法，JVM实现了多个垃圾收集器，可以通过一些参数进行设定。**

#### Object类 

![Object类.png](https://github.com/g453030291/building-java-tower/blob/master/images/Object类.png)

![Object类方法分类.png](https://github.com/g453030291/building-java-tower/blob/master/images/Object类方法分类.png)

#### 深拷贝和浅拷贝

浅拷贝

````java
public class Screen implements Cloneable{

    /**
     * 大小
     */
    private int size;

    public Screen() {
    }

    public Screen(int size) {
        this.size = size;
    }

    public int getSize() {
        return size;
    }

    public void setSize(int size) {
        this.size = size;
    }

    @Override
    public String toString() {
        return "Screen{" +
                "size=" + size +
                '}';
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

````

````java
public class Phone implements Cloneable {

    /**
     * 品牌
     */
    private String brand;

    /**
     * 序列号
     */
    private long serialNum;

    /**
     * 屏幕
     */
    private Screen screen;

    public Phone() {
    }

    public Phone(String brand, long serialNum, Screen screen) {
        this.brand = brand;
        this.serialNum = serialNum;
        this.screen = screen;
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public long getSerialNum() {
        return serialNum;
    }

    public void setSerialNum(long serialNum) {
        this.serialNum = serialNum;
    }

    public Screen getScreen() {
        return screen;
    }

    public void setScreen(Screen screen) {
        this.screen = screen;
    }

    @Override
    public String toString() {
        return "Phone{" +
                "brand='" + brand + '\'' +
                ", serialNum=" + serialNum +
                ", screen=" + screen +
                '}';
    }
//浅拷贝
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
 //深拷贝
        @Override
    protected Object clone() throws CloneNotSupportedException {
        Phone phone = (Phone) super.clone();
        phone.setScreen((Screen) screen.clone());
        return phone;
    }

}

````

测试

````java
    public static void main(String[] args)throws Exception {
        Screen screen = new Screen(5);
        Phone phone1 = new Phone("apple",1l,screen);
        Phone phone2 = (Phone) phone1.clone();

        System.out.println(phone1.getScreen());
        System.out.println(phone2.getScreen());

        screen.setSize(6);

        System.out.println(phone1.getScreen());
        System.out.println(phone2.getScreen());

    }
````



#### 数组特点

- java的内建类型；
- 大小类型固定、性能高效；
- 支持边界值检查；
- length只能反映最大容量，不能反映使用的大小；
- java.util.Arrays提供了fill、set、sort、binarySearch、equals、ahshCode、toString、parallerlPrefix、copyOf等方法；
- 日常开发中使用更多的jdk提供的容器工具类，只有当确定容器工具类成为了系统的瓶颈的时候，才会直接使用数组；

#### Collection类

##### 一、List：

![List类图.png](https://github.com/g453030291/building-java-tower/blob/master/images/List类图.png)

List表示了一系列有序的对象；

线程安全：Vector、ConpyOnWriterArrayList（分段锁）

非线程安全：ArrayList、LinkedList

##### 二、Set：

![Set类图.png](https://github.com/g453030291/building-java-tower/blob/master/images/Set类图.png)

内部实现有序：TreeSet、ConcurrentSkipListSet

内部实现无序：HashSet（HashMap实现）、LinkedHashSet（实现插入顺序为保存顺序）

线程安全：ConcurrentSkipListSet（ConcurrentSkipListMap实现）、ConpyOnWriteArraySet（CopyOnWriterArrayList实现）

非线程安全：TreeSet（TreeMap实现）、HashSet（HashMap实现）

##### 三、Queue：

![Queue类图.png](https://github.com/g453030291/building-java-tower/blob/master/images/Queue类图.png)

代表一个队列；

阻塞队列：BlockingQueue（接口）

队列两端都可以存取：Deque

线程安全：PriorityBlockingQueue（优先队列）

非线程安全：PriorityQueue（优先队列）

延时队列：DelayQueue

实现生产者消费者模式：SynchronousQueue、TransferQueue

##### 四、Map

![Map类图.png](https://github.com/g453030291/building-java-tower/blob/master/images/Map类图.png)

有序（按照key排序）：TreeMap（实现了SortedMap）、ConcurrentSkipListMap

线程安全：ConcurrentSkipListMap、ConcurrentHashMap（分段锁）

非线程安全：HashMap、WeakHashMap（弱引用）、LinkedHashMap、IdentityHashMap（严格对比两个key的内存地址是否相同判断是否同一个对象）

#### 重写equals，就必须重写hashcode

hashcode：如果两个对象相等，那么这两个对象的hash码也必须相等

hashcode可以用于散列数据的快速存取（HashMap、HashSet......这种容器都是通过使用hash值来判断两个对象是否是同一个对象）

euquals：两个对象的成员变量值相等，那么这两个对象就是相等的

#### 使用Map

使用Map的方法keySet()/values()/entrySet()返回集合对象时，不可以对其进行添加元素操作，否则会抛出UnsupportedOperationException异常

#### Collections类

Collections类返回的对象，如：emptyList()/singletonList()等都是immutable list，不可对其进行添加或者删除元素的操作

#### ArrayList类

ArrayList的subList结果不可强转成ArrayList，否则会抛出ClassCastException异常

#### subList方法

在subList场景中，高度注意对父集合元素个数的修改，会导致字列表的遍历、增加、删除均会产生ConcurrentModeficationException异常

#### 集合转数组

使用集合转数组的方法，必须使用集合的toArray(T[] array)传入的是类型完全一样的数组，大小就是list.size()。（如果直接使用没有参数的toArray()方法，会返回一个object类型的数组，如果强制类型转换，会抛出ClassCastException。使用了toArray(T[] array)方法后，入参分配的数组空间不够大的时候toArray(T[] array)方法内部将会重新分配内存空间，并返回新数组的内存地址。如果数组元素的个数大于了实际所需，下标为list.size()到数组末尾的全部元素会被置为null。最好将方法入参数组的大小设置为list大小一致）

#### Arrays.AsList()方法

实用工具类Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方法，它的add/remove/clear方法会抛出UnsupportedOperationException异常。（这个方法内部使用的list类并不是java.util.ArrayList这个类，他是使用的是自己内部实现的ArrayList，这个类并没有重写list抽象类中的add/remove/clear方法）

#### foreach循环

不要再foreach循环里进行元素的remove/add操作，remove元素请使用Iterator方式，如果并发操作，需要对Iterator对象加锁

#### 泛型

解决了三个问题：

①：编译期类型检查（类型参数化）

​	比如你声明一个list，如果没有泛型，你可以在list中存储String、Integer等很多种类型的对象。但是在你使用的时候，会导致报错，出现异常。

②：强制类型转换

​	如果没有泛型，可能在使用的时候，需要强制类型转换来获得自己想要的类型。使用泛型，可以避免这种操作。

③：可读性和灵活性

​	如果不使用泛型，当一个不清楚业务场景的人在对集合进行操作时，无法知道list中存储的是什么类型的对象，如果使用了泛型，就能够通过其类型参数判断出当前的业务场景，也增加了代码的可读性，同时也可以大胆地在抽象继承的基础上进行开发了 

##### 限定通配符、非限定通配符

一共有三种：<? extends T>、<? super ?>、<?>

1.上界通配符<? extends T>：

​	add方法会受限，get方法不会。因为add的时候，编译器无法去确定你添加的元素到底是什么类型。但是get的时候，可以将你取出的元素赋值给父类型去接受。

2.下界通配符<? super ?>：

​	get方法受限，add方法不会。可以addT和T的子类。但是get的时候，无法判断get的元素类型。

3.无界通配符<?>：

​	表示任意一种类型。注意List<Object>和List<?>是不一样的。	前者Object表示继承树的最顶层，后者包含null，是完全不可知的类型。

#### 反射

①：自控制：构造对象并控制对象的行为

②：自描述：通过反射获取类、属性、方法的信息

反射的优势：

1.在一些场景中，这种“未知类型”大大增强了程序运行时的灵活性，但是对性能有一些损耗

2.对于对象的类型可以在运行时判断，这样的特性实际上是对多态极大的增强，进一步的将上层的抽象与下层的具体实现进行解耦

#### 注解

![Java注解.png](https://github.com/g453030291/building-java-tower/blob/master/images/Java注解.png)

四个元注解：

@Target：解决了方法作用域的问题

@Retention：解决了作用阶段的问题

@Documented：JavaDoc文档支持

@Inherited：解决了继承关系的问题

#### Lambda

​	Lambda可以替代特定匿名内部类，Lambda表达式不能单独存在，在使用时必须继承函数式接口。 

​	Java8中提供了注解@FunctionalInterface检验接口是否为函数式接口

​	Lambda可以替代特定匿名内部类，简化代码，但是必须继承函数式接口。  

```java
package lambda;

public class Lambda {

    public static void main(String[] args) {

        Flyable flyable = new Flyable() {
            @Override
            public void fly(int a) {
                System.out.println("I can fly by anonymous class");
            }

            //@Override
            //public void landing() {
            //    System.out.println("I can landing by anonymous class");
            //}
        };
        flyable.fly(1);

        flyable = (t) -> System.out.println("I can fly by lambda");
        flyable.fly(1);


        Bird bird = new Bird() {
            @Override
            void fly() {
                System.out.println("I can fly by bird");
            }
        };

        bird = () -> System.out.println("I can fly by lambda");

    }

    @FunctionalInterface
    interface  Flyable {
        void fly(int a);
        //void landing();
    }

    abstract static class Bird {
        abstract void fly();
    }
}
```

#### 多线程技术

​	进程：进程是具有一定独立功能的程序，关于某个数据集合上的一次运行活动，是系统进行资源分配和调度的一个独立单位。

​	线程：是进程的一个实体，是CPU分配调度的基本单位，代码的执行体。

##### 线程的生命周期：

![线程状态图.png](https://github.com/g453030291/building-java-tower/blob/master/images/线程状态图.png)

##### 线程并行与协作：

卖票问题代码示例：(存在重复售票的问题)

```java
public class Tickets {

    int tickets = 10;

    /**
     * 重复卖票
     */
    void sell() {
        while (tickets > 0) {
            System.out.println(Thread.currentThread().getName() + " sell ticket:" + tickets);
            tickets--;
        }
        System.out.println(Thread.currentThread().getName() + " sell out.");
    }
    public static void main(String[] args) {
        Tickets tickets = new Tickets();

        Thread sellerA = new Thread(tickets::sell);
        sellerA.setName("sellerA");

        Thread sellerB = new Thread(tickets::sell);
        sellerB.setName("sellerB");

        Thread sellerC = new Thread(tickets::sell);
        sellerC.setName("sellerC");

        sellerA.start();
        sellerB.start();
        sellerC.start();
    }

}
```

解决方案1：synchronized(加锁后被A独占)

```java
   synchronized void sell() {
        while (tickets > 0) {
            System.out.println(Thread.currentThread().getName() + " sell ticket:" + tickets);
            tickets--;
        }
        System.out.println(Thread.currentThread().getName() + " sell out.");
    }
```

解决方案2：解决独占问题（会产生卖超）

```java
void sell() {
        while (tickets > 0) {
            synchronized (Tickets.class){
                System.out.println(Thread.currentThread().getName() + " sell ticket:" + tickets);
                tickets--;
            }

            try {
                TimeUnit.MILLISECONDS.sleep(50L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
        System.out.println(Thread.currentThread().getName() + " sell out.");
    }
```

解决方案3：解决超卖的问题

```java
void sell() {
        while (tickets > 0) {
            synchronized (this){
                System.out.println(Thread.currentThread().getName() + " sell ticket:" + tickets);
                tickets--;
            }

            try {
                TimeUnit.MILLISECONDS.sleep(50L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
        System.out.println(Thread.currentThread().getName() + " sell out.");
    }
```

解决方案4：使用ReentrantLock提高性能（分段锁）

```java
private Lock lock = new ReentrantLock();
    /**
     * 使用锁
     */
    void sell() {
        while (tickets > 0) {
            lock.lock();
            try {
                if (tickets > 0) {
                    System.out.println(Thread.currentThread().getName() + " sell ticket:" + tickets);
                    tickets--;
                }
            } finally {
                lock.unlock(); //锁必须在finally块中释放
            }

            //do something
            try {
                TimeUnit.MILLISECONDS.sleep(50L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
        System.out.println(Thread.currentThread().getName() + " sell out");
    }
```

##### 线程池：

​	在一个JVM里创建太多线程，可能会导致系统由于过度消耗内存导致系统资源不足，为了防止资源不足，应该尽可能减少创建和销毁线程的次数，特别是一些资源耗费比较大的线程的创建和销毁，尽量复用已有对象来进行服务，这就线程池技术产生的原因 。如果想要实现线程的复用，我们需要继承线程，在run方法中通过循环不断从外部获取runnable的实现，以此达到线程复用的目的。 

ThreadPoolExecutor 线程池

- corePoolSize：表示线程池的核心线程数，指线程池中常驻线程的数量，核心线程数会一直在线程池中存活，除非线程池停止使用被资源回收了。

- maximumPoolSize：指线程池所能容纳的最大线程数量，当活动线程数到达这个数值后，后续的新任务将会被阻塞。

- keepAliveTime：非核心线程闲置时的超时时长，超过这个时长，非核心线程就会被回收。当ThreadPoolExecutor的allowCoreThreadTimeOut属性设置为true时，keepAliveTime同样会作用于核心线程。

- Unit：用于指定keepAliveTime参数的时间单位。

- workQueue：表示线程池中的任务队列（阻塞队列），通过线程池的execute方法提交Runnable对象会存储在这个队列中。

- threadFactory：表示线程工厂，为线程池提供创建新线程的功能。

- RejectExecutionHandler：这个参数表示当ThreadPoolExecutor已经关闭或者已经饱和时（达到了最大线程池大小而且工作队列已经满），提供以下几个策略考虑是否拒绝到达的任务。（DiscardPolicy：直接忽略提交的任务

  。AbortPolicy：忽略提交的任务，在拒绝的同时抛出异常，通知调用者拒绝执行

  。CallerRunsPolicy：让线程池的使用者所在的线程运行提交的任务调用者

  。DiscardOlderestPolicy：忽略最早放到队列中的任务）

  ```java
  //线程池参考实现
   ThreadPoolExecutor threadPoolExecutor =
              new ThreadPoolExecutor(16, 30, 30L, TimeUnit.SECONDS,
                  new ArrayBlockingQueue<Runnable>(10), new ThreadFactory() {
                  @Override
                  public Thread newThread(Runnable r) {
  
                      Thread t = new Thread(r);
  
                      t.setDaemon(false);
                      t.setUncaughtExceptionHandler((thread, e) -> System.out.println(e.getMessage()));
                      return t;
                  }
              }, new DiscardOldestPolicy());
  
  
  
          threadPoolExecutor.submit(() -> System.out.println(Thread.currentThread().getName()));
  
          threadPoolExecutor.shutdown();
  ```

  注意：不推荐使用Java自带的线程池实现，因为Java自带的线程池都是有bug的。

  

```java
 static void findJavaExecutorsBug() {
       ExecutorService executorService = Executors.newSingleThreadExecutor();

       for (;;) {
           executorService.submit(() -> {
               try {
                   TimeUnit.SECONDS.sleep(30);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           });
       }
    }
```

代码利用循环，无限添加runnable的实现，但是由于单一线程的阻塞队列是没有边界的，会导致添加的对象过多，耗尽内存资源。 

#### 扩展阅读

《Java编程思想》

《深入理解Java虚拟机》

源码阅读：

java.util.LinkedList

java.util.ArrayList

java.util.TreeSet

java.util.HashSet

java.util.TreeMap

java.util.HashMap

《Java开发手册》





注：本篇文章内容，均来自于阿里巴巴云栖社区直播课，Java基础入门、Java高级特性入门课程总结。