#### Java中的异常总结

Java中所有异常和错误的超类是Throwable，它有两个字类Error和Exception。

![Throwable.png](https://github.com/g453030291/building-java-tower/blob/master/images/Throwable.png)

Error是严重到系统不能处理的系统类错误，如：内存溢出、虚拟机错误、栈溢出等。这类错误一般由硬件引起，通常通知系统去处理，程序无法捕获处理。

Exception是用于捕获编写程序时无法预料的情况，如中断异常、非法存取等。为了保证程序的健壮性，java要求必须对这些可能出现的异常进行捕获，并对其进行处理。

RuntimeException是Exception的子类。是那些可能在java虚拟机运行期间抛出异常的超类。

#### Java中的异常通常分为两类

##### 一：检查异常(checked exceptions)

​	编写代码的过程中，编译器要求你必须处理的异常（SQLException , IOException,ClassNotFoundException）。这种异常就是编译时异常。通常使用try...catch、throws exception来处理。

为什么要有这样的异常处理机制？

​	因为程序运行在各种未知的环境下，而程序员无法干预用户如何使用他编写的程序，所有程序员要捕获各种异常，来应对可能发生的各种情况，避免程序出错导致系统崩溃。

##### 二：非检查异常(unchecked exceptions)

​	Error类或者RuntimeException类及其子类，为非检查异常。

#### try-with-resource

​	对于try-finally这种形式的代码，可以使用jdk1.7新增的try-with-resource方式来代替，这种方式需要resource实现AutoCloseable接口，实现资源的自动释放。

例：

 ```java
public static void main(String[] args) {
        try(Connection conn =new Connection()) {
            conn.sendData();
        }catch(Exception e) {
            e.printStackTrace();
        }
    }
 ```

之前使用的try-finally形式，如果有多个异常抛出，只能看到最后的异常，之前的异常会被丢失，无法查看。使用try-with-resource形式，可以避免这种问题，并且无需显示关闭资源，jdk会为你自动加上finally块来close你所使用的所有的资源。

 

 

 

 