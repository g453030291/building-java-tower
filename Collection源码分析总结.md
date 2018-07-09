#### HashMap：

HashMap是一个用于存储Key-Value键值对的集合，其实就是一个个的Entry对象。这些Entry分散存储在一个数组中，HashMap每一个初始值都是Null。

Put()：对key进行hash计算index，存储到数组。如果发生index冲突，hashmap的每个元素都是一个链表的头节点，每一个新来的entry都会存储在链表中。每次插入的entry使用的是头插法，hashmap的作者认为，新插入的entry被查找的可能性更大。

Get()：对key进行hash计算得到index，然后根据index，去相应的位置(可能是在链表中一一查找)，查找对应的结果。

 

Key经过hash方法得到一个槽的索引，槽中保存数据

非线程安全

Key，value可以为null

Cllections.synchronizedMap可以得到一个线程安全的map

Hashmap中存放的是entry对象

 

 

 

初始化容量

加载因子系数：达到0.75的时候扩容

默认最大容量

 

Key可以为空，null作为key

如果key重复了，old value会被覆盖

Put的时候进行扩容

 

 

 

#### 常见HashMap面试题

我们现在可以回答开始的几个问题，加深对HashMap的理解：

 

什么时候会使用HashMap？他有什么特点？

 

是基于Map接口的实现，存储键值对时，它可以接收null的键值，是非同步的，HashMap存储着Entry(hash, key, value, next)对象。

 

 

你知道HashMap的工作原理吗？

 

通过hash的方法，通过put和get存储和获取对象。存储对象时，我们将K/V传给put方法时，它调用hashCode计算hash从而得到bucket位置，进一步存储，HashMap会根据当前bucket的占用情况自动调整容量(超过Load Facotr则resize为原来的2倍)。获取对象时，我们将K传给get，它调用hashCode计算hash从而得到bucket位置，并进一步调用equals()方法确定键值对。如果发生碰撞的时候，Hashmap通过链表将产生碰撞冲突的元素组织起来，在[Java ](http://lib.csdn.net/base/java)8中，如果一个bucket中碰撞冲突的元素超过某个限制(默认是8)，则使用红黑树来替换链表，从而提高速度。

 

 

你知道get和put的原理吗？equals()和hashCode()的都有什么作用？

 

通过对key的hashCode()进行hashing，并计算下标( n-1 & hash)，从而获得buckets的位置。如果产生碰撞，则利用key.equals()方法去链表或树中去查找对应的节点

 

 

你知道hash的实现吗？为什么要这样实现？

 

在Java 1.8的实现中，是通过hashCode()的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)，主要是从速度、功效、质量来考虑的，这么做可以在bucket的n比较小的时候，也能保证考虑到高低bit都参与到hash的计算中，同时不会有太大的开销。

 

 

如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？

 

如果超过了负载因子(默认0.75)，则会重新resize一个原来长度两倍的HashMap，并且重新调用hash方法。 
关于Java集合的小抄中是这样描述的： 
以Entry[]数组实现的哈希桶数组，用Key的哈希值取模桶数组的大小可得到数组下标。 
插入元素时，如果两条Key落在同一个桶(比如哈希值1和17取模16后都属于第一个哈希桶)，Entry用一个next属性实现多个Entry以单向链表存放，后入桶的Entry将next指向桶当前的Entry。 
查找哈希值为17的key时，先定位到第一个哈希桶，然后以链表遍历桶里所有元素，逐个比较其key值。 
当Entry数量达到桶数量的75%时(很多文章说使用的桶数量达到了75%，但看代码不是)，会成倍扩容桶数组，并重新分配所有原来的Entry，所以这里也最好有个预估值。 
取模用位运算(hash & (arrayLength-1))会比较快，所以数组的大小永远是2的N次方， 你随便给一个初始值比如17会转为32。默认第一次放入元素时的初始值是16。 
iterator()时顺着哈希桶数组来遍历，看起来是个乱序。

 

 

当两个对象的hashcode相同会发生什么？

 

因为hashcode相同，所以它们的bucket位置相同，‘碰撞’会发生。因为HashMap使用链表存储对象，这个Entry(包含有键值对的Map.Entry对象)会存储在链表中。

 

 

如果两个键的hashcode相同，你如何获取值对象？

 

找到bucket位置之后，会调用keys.equals()方法去找到链表中正确的节点，最终找到要找的值对象。因此，设计HashMap的key类型时，如果使用不可变的、声明作final的对象，并且采用合适的equals()和hashCode()方法的话，将会减少碰撞的发生，提高效率。不可变性能够缓存不同键的hashcode，这将提高整个获取对象的速度，使用String，Interger这样的wrapper类作为键是非常好的选择

 

 

如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？

 

默认的负载因子大小为0.75，也就是说，当一个map填满了75%的bucket时候，和其它集合类(如ArrayList等)一样，将会创建原来HashMap大小的两倍的bucket数组，来重新调整map的大小，并将原来的对象放入新的bucket数组中。这个过程叫作rehashing，因为它调用hash方法找到新的bucket位置

 

 

你了解重新调整HashMap大小存在什么问题吗？

 

当重新调整HashMap大小的时候，确实存在条件竞争，因为如果两个线程都发现HashMap需要重新调整大小了，它们会同时试着调整大小。在调整大小的过程中，存储在链表中的元素的次序会反过来，因为移动到新的bucket位置的时候，HashMap并不会将元素放在链表的尾部，而是放在头部，这是为了避免尾部遍历(tail traversing)。如果条件竞争发生了，那么就死循环了。因此在并发环境下，我们使用CurrentHashMap来替代HashMap

 

 

为什么String, Interger这样的wrapper类适合作为键？

 

因为String是不可变的，也是final的，而且已经重写了equals()和hashCode()方法了。其他的wrapper类也有这个特点。不可变性是必要的，因为为了要计算hashCode()，就要防止键值改变，如果键值在放入时和获取时返回不同的hashcode的话，那么就不能从HashMap中找到你想要的对象。不可变性还有其他的优点如线程安全。如果你可以仅仅通过将某个field声明成final就能保证hashCode是不变的，那么请这么做吧。因为获取对象的时候要用到equals()和hashCode()方法，那么键对象正确的重写这两个方法是非常重要的。如果两个不相等的对象返回不同的hashcode的话，那么碰撞的几率就会小些，这样就能提高HashMap的性能

 

 

 

 

 

#### Arraylist：

Arraylist存放的是对象的引用，而不是对象本身

默认大小为10

list.add(e);

默认从尾部开始添加元素

add方法先调用ensureCapacityInternal方法增加自身容量，

如果扩容后的容量比默认的10小，那么list的容量将强制更改为默认的10，也就是说只要调用了add方法，list的容量至少为10。

​        if扩容后的容量超出了数组可容纳的长度则执行grow操作，新的容量为旧的容量的1.5倍，右移操作即除以2（为什么用右移，而不直接除以2 ，这是给电脑运算的，运算的应该是二进制效率才最快，而不是给人类运算的，人类运算的才是十进制）。

​        grow：如果Arraylist的容量超过默认最大值，允许其增加为Integer.MAX_VALUE，即再次增加8。

modCount：

​        从类 java.util.AbstractList 继承的字段，protected transient int modCount，表示已从结构上修改此列表的次数。从结构上修改是指更改列表的大小，或者打乱列表，从而使正在进行的迭代产生错误的结果。 

​        在使用迭代器遍历的时候，用来检查列表中的元素是否发生结构性变化（列表元素数量发生改变）了，主要在多线程环境下需要使用，防止一个线程正在迭代遍历，另一个线程修改了这个列表的结构。使用ArrayList中的remove(int index) remove(Object o) remove(int fromIndex ,int toIndex) add等方法的时候都会修改modCount，在迭代的时候需要保持单线程的唯一操作，如果期间进行了插入或者删除，就会被迭代器检查获知，从而出现运行时异常。具体why？？？

ArrayList是非线程安全的。

 

list.add(index, element);

(此方法性能较差,因为移动元素的时候会调用arrayCopy方法)

在指定位置插入指定元素。将当前处于该位置的元素（如果有的话）和所有后续元素向后移动（其索引加 1）。

①先调用rangeCheckForAdd检查索引是否合法；

②合法再调用上面讲过的ensureCapacityInternal执行扩容；

③调用系统的arraycopy函数将索引处及其后的元素后移一位；

④在index处插入该元素。

 

#### LinkedList:

基于链表实现的

插入、删除操作比ArrayList更加高效

随机访问的效率小比ArrayList差

数据结构：

LinkedList 是基于链表结构实现，所以在类中包含了 first 和 last 两个指针(Node)。Node 中包含了上一个节点和下一个节点的引用，这样就构成了双向的链表。每个 Node 只能知道自己的前一个节点和后一个节点，但对于链表来说，这已经足够了。

Add方法会默认添加到链表的尾部

 

#### Hashset

非线程安全

允许null值

添加值得时候会先获取对象的hashCode方法，如果hashCode 方法返回的值一致，则再调用equals方法判断是否一致，如果不一致才add元素。

value使用一个static final的Object对象标识。  

 

 

#### Linkedhashmap

LinkedHashMap的核心就是存在存储顺序和可以实现LRU算法，所以下面我会用两个demo先来证明这两种情况：

LRU算法。有一个重载的构造方法。开启，参数true。可以实现，最近访问的数据（get或put）放在链表末尾。

 

 

 

#### Queue

队列使用堆排序，二叉树，使用数组保存数据，非线程安全