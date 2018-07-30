#### JVM的参数分类：

HotSpot JVM提供了三类参数。

##### 一、标准参数（以-开头）：

标准参数：可以用java命令java -help检索出所有的标准参数。如-server

##### 二、X参数（以-X开头）：

非标准化的参数，这类参数在将来的版本中都有可能会改变。可以使用java -X检索

##### 三、XX参数(以-XX开头)：

XX参数，很多还在实验当中（主要是JVM的开发者用于debug和调优JVM自身使用）。

语法：用一句话来说明XX参数的语法。所有的XX参数都以”-XX:”开始，但是随后的语法不同，取决于参数的类型。

- 对于布尔类型的参数，我们有”+”或”-“，然后才设置JVM选项的实际名称。例如，-XX:+<name>用于激活<name>选项，而-XX:-<name>用于注销选项。

- 对于需要非布尔值的参数，如string或者integer，我们先写参数的名称，后面加上”=”，最后赋值。例如，  -XX:<name>=<value>给<name>赋值<value>。

  （参考http://ifeve.com/useful-jvm-flags-part-2-flag/）

#### **-server and -client** 

HotSpot JVM分为server和client，server中的JVM为堆分配更大的空间以及一个并行的垃圾收集器，运行时可以最大程度优化代码。

#### **-version and -showversion** 

showversion对所有的java命令都是有效的补充，启动java程序时可以添加这个参数

#### jps

JVM Process Status Tool，显示指定系统内所有得HotSpot虚拟机进程

#### jstack

例：jstack -l 8264(java进程号，通过jps获得)

生成java虚拟机当前时刻的线程快照。线程快照是当前java虚拟机内每一条线程正在执行方法堆栈的集合，目的是定位线程出现长时间停顿的原因，如线程死锁、死循环等。通过jstack查看各个线程调用的堆栈，就可以知道没有响应的线程在后台做什么事。如果线程出现hung的状态，jstack是很有用的。

常用方法是，间隔几秒执行两次stack来对比查看线程状态。

#### jstat

例：jstat -gcutil 8264 1000 5（每间隔1000毫秒，打印5次，进程为8264的gc情况）

JVM statistics Monitoring，用来监视JVM运行时状态，他可以显示出虚拟机进程中的类装载、内存、垃圾手机、JIT编译等运行数据。

#### jmap

例：jmap -histo:live 8264

生成dump文件：jmap -dump:format=b,file=d:/dump0627.dat 8264

JVM Memory Map，用于成城heap dump文件。可以使用-XX：+HeapDumpOnOutOfMemoryError参数来让虚拟机出现OOM时，自动生成dump文件。

jmap还能查询finalize执行队列、java堆和永久代的详细信息，如当前使用率、当前使用的那种收集器等。

#### jhat

例：jhat -port 9998 d:/dump0627.dat

JVM Heap Analysis Tool，用来分析jmap生成的dump，jhat内置了一个http服务器，可以在浏览器中访问http://localhost:9998查看

(可以使用Eclipse Memory Analysis图形化工具来分析dump文件)

#### jinfo

例：jinfo -flags 8264

JVM Configuration info，实时查看和调整虚拟机运行参数

