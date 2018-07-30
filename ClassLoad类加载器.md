ClassLoader（类加载器分两种）：

Bootstrap ClassLoader（启动类加载器）：由c++编写，加载jdk自带的类库

其他类加载器：

​	①：Extension ClassLoader（扩展类加载器）：由java实现，继承自抽象类ClassLoader

​	②：Application ClassLoader（应用程序类加载器）：负责加载用户指定路径上的指定类库，默认情况下，用户没有指定类加载器，就是用这个作为默认的类加载器

双亲委派模型工作工程：

​	如果一个类加载器收到类加载的请求，他首先不会自己去尝试加载这个类，而是会把这个请求委派给父类加载器完成。只有当父类加载器范围内找不到指定的类时（抛出ClassNotFoundException），子加载器才会自己去尝试加载。

![双亲委派模型.png](https://github.com/g453030291/building-java-tower/blob/master/images/双亲委派模型.png)