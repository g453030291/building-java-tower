MySQL优化原则：

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



windows 下操作mysql（bin目录下）
登录：mysql -u root -p
启动：net start mysql
停止：net stop mysql
退出：exit
修改密码：SET PASSWORD = PASSWORD('your_new_password');
查看mysql默认编码：show variables like 'character_set%';
查看mysql默认字符集:show variables like 'character_set%';
查看mysql版本：select version();