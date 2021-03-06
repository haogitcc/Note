## 数据库常见问题 ##

### <b>外键的性能影响</b> ###

1. 超大型的数据应用场景，大量的插入，更新和删除在外键的约束下会降低性能
2. 数据库在水平拆分和分库的情况下，数据库端也做不到执行外键约束
3. 在高并发的情况下，每次更新数据，外键都需要检查另外一张表的数据，容易造成死锁

### <b>COUNT()的效率</b> ###
1. MyISAM 的数据表都有一个meta信息有存储了row_count. 统计数据表的行数只需要O(1)复杂度, 一致性由表级锁来保证.
2. InnoDB支持事务，采用行级锁和MVCC机制,无法维护row_count, COUNT(*)采用全表扫描，进行循环+计数的方式进行统计.
3. 在InnoDB引擎中，采用COUNT(*)和COUNT(1)来统计数据行数，要尽量采用二级索引.
4. 查找行用主键索引【聚簇索引】
5. 不查找具体的行用占用空间更小的二级索引【非聚簇索引】
6. COUNT(*) = COUNT(1) > COUNT(字段)

### <b>聚集函数问题集合</b>###
1. COUNT(role_assist)会忽略值为NULL的数据行，而 COUNT(*) 不会NULL

### <b>语法问题集合</b> ###
1. 使用表的别名，在查询字段中就只能使用别名进行代替，不能使用原有的表名
2. SQL语句最后以分号（;）作为结束符
3. 表创建SQL语句最后一个字段的定义结束后没有逗号

### <b>数据库可移植性问题</b> ###
- MySQL在Linux的环境下，数据库名、表名、变量名是严格区分大小写的 
- MySQL在Windows的环境下全部不区分大小写
- MySQL在Windows/Linux的环境下字段名是忽略大小
- 建议格式：
    1. 关键字和函数名称全部大写
    2. 数据库名、表名、字段名称全部小写

### <b>SQL语句连接查询执行顺序</b> ###
- 先进行笛卡尔积
- FROM(ON) > WHERE > GROUP BY > (聚合函数) > HAVING > SELECT 的字段 > DISTINCT > ORDER BY > LIMIT

### <b>SQL视图相关问题集</b> ###
- SQLite 不支持视图的修改，仅支持只读视图。如果想要修改视图，就需要先 DROP 然后再 CREATE。
- 有些DBMS不支持的视图不支持索引，MYSQL支持。

### <b>SQL事务问题集</b> ###
- Oracle 事务默认不自动提交，需要手写 COMMIT 命令，MySQL 默认自动提交
- 脏读、不可重复读和幻读这三种异常情况是在SQL-92标准中定义的，同时SQL-92标准还定义了4种隔离级别来解决这些异常情况。

### <b>mysql索引相关问题</b> ###
- 在 MySQL 中支持两种排序方式，分别是 FileSort 和 Index 排序。
- 在 Index 排序中，索引可以保证数据的有序性，不需要再进行排序，效率更高。
- FileSort 排序则一般在内存中进行排序，占用 CPU 较多。如果待排结果较大，会产生临时文件 I/O 到磁盘进行排序的情况，效率较低。
- 避免全表扫描在 WHERE 子句和 ORDER BY 子句中使用索引>
- ORDER BY排序: 单索引> 联合索引 > FileSort
- 什么是自适应hash索引？
    1. MySQL提供了一个自适当Hash索引的功能（Adaptive Hash index）
    2. 自适应指的是不需要人工来制定，而是系统根据情况来自动完成的
    3. 某个数据经常会访问到，当满足一定条件的时候，就会将这个数据页的地址存放到Hash表中
    4. 自适应哈希索引只保存热数据（经常被使用到的数据），并非全表数据。因此数据量并不会很大，可以让自适应Hash放到缓冲池中，也就是InnoDB buffer pool，进一步提升查找效率。
    5. InnoDB中的自适应Hash相当于是“索引的索引”，采用Hash索引存储的是B+树索引中的页面的地址。
    6. 通过innodb_adaptive_hash_index变量来查看是否开启了自适应Hash。比如：mysql> show variables like '%adaptive_hash_index';
    7. MySQL InnoDB 中，系统默认单个索引长度最大为 767 bytes，如果单超过这个限制，取前 255 字符


- 普通索引和唯一索引在查询效率上有什么不同呢？
    1. 唯一索引：在普通索引上增加了约束性，也就是关键字唯一，找到了关键字就停止检索。
    2. 普通索引：将这个记录所在的页加载到内存中，在内存中多几次“判断下一条记录”的操作。性能差别不大

### <b>Mysql InnoDB 三大特性？</b> ###
- 自适应Hash，插入缓冲（Insert Buffer），二次写(Double Write)。

### <b>各种数据库页大小</b> ###
- Mysql InnoDB引擎默认支持16K
- SQL Server 的页大小为 8KB
- Oracle 中我们用术语“块”（Block）来代表“页”，Oralce 支持的块大小为 2KB，4KB，8KB，16KB，32KB 和 64KB。

### <b>为什么对权限的检查不在优化器之前做？</b> ###
- SQL语句要操作的表不只是SQL字面上那些。比如如果有个触发器，得在执行器阶段（过程中）才能确定。