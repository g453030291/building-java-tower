#### MySQL优化原则

1.全值匹配我最爱

2.最佳左前缀法则

3.不在索引列上做任何操作（计算、函数、（自动or手动）类型转换），会导致索引失效而转向全表扫描

4.存储引擎不能使用索引中范围条件右边的列

5.尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少select *

6.mysql在使用不等于（！=或者<>）时无法使用索引会导致全表扫描

7.is null，is not null也无法使用索引

8.like以通配符开头（'%abc...'）mysql索引失效会变成全表扫描的操作

9.字符串不加单引号索引失效

10.少用or，用它来连接时会索引失效



#### windows 下操作mysql（bin目录下）

登录：mysql -u root -p
启动：net start mysql
停止：net stop mysql
退出：exit
修改密码：SET PASSWORD = PASSWORD('your_new_password');
查看mysql默认编码：show variables like 'character_set%';
查看mysql默认字符集:show variables like 'character_set%';
查看mysql版本：select version();



#### mysql开启远程访问权限

1.本地命令行界面连接mysql：mysql -u root -p
2.选择mysql数据库：use mysql；
3.配置某个ip使用账户/密码访问：GRANT ALL PRIVILEGES ON *.* to 'root'@'192.168.1.10' identified by '123456';
4.刷新权限配置：flush privileges;
5.查看权限状态：select host,user from user;





#### Mysql的4种隔离级别：

READ UNCOMMITTED:脏读，可以读到其他事务未提交的数据

READ COMMITTED:读已经提交的数据（同一事物中，查询统一数据，结果可能不同）

REPEATABLE READ（repeatable read）:mysql默认的隔离级别（同一事物里，查询同一数据结果一定相同）

SERIALIZABLE（serializable）：线性的事务规则。所有事物要排队一一执行。



