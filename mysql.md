# 索引
"""
索引可以理解为排好序的快速查找的数据结构 用的比较多的B+TREE
索引文件存储在磁盘上
优点：
    可以降低IO成本，提高数据检索的效率
    降低CPU消耗，降低数据的排序成本
缺点：
    索引是一张表，占空间
    降低更新表的速度，因为更新表的时候会重新建（维护）索引
   
一个字段中的值重复率太高的时候没有必要建立索引，比如性别


"""

# explain
"""
 explain 分析sql语句，查询执行计划
 ID，表的执行顺序 ID越大越优先被执行
 type，索引调优 system>const>eq_ref>ref>range>index>all
 key,用到的索引
 key_len,索引字段最大可能长度
 rows,读取的行数
 extra 额外的
 可以分析性能
"""
# 引擎
"""
InnoDB支持事务，MyISAM不支持。
MyISAM适合查询以及插入为主的应用，InnoDB适合频繁修改以及涉及到安全性较高的应用。
InnoDB支持外键，MyISAM不支持。
从MySQL5.5.5以后，InnoDB是默认引擎。
MyISAM支持全文类型索引，而InnoDB不支持全文索引。
InnoDB中不保存表的总行数，select count(*) from table时，InnoDB需要扫描整个表计算有多少行，但MyISAM只需简单读出保存好的总行数即可。注：当count(*)语句包含where条件时MyISAM也需扫描整个表。
对于自增长的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中可以和其他字段一起建立联合索引。
清空整个表时，InnoDB是一行一行的删除，效率非常慢。MyISAM则会重建表。MyisAM使用delete语句删除后并不会立刻清理磁盘空间，需要定时清理，命令：OPTIMIZE table dept;
InnoDB支持行锁（某些情况下还是锁整表，如 update table set a=1 where user like ‘%lee%’）
Myisam创建表生成三个文件：.frm 数据表结构 、 .myd 数据文件 、 .myi 索引文件，Innodb只生成一个 .frm文件，数据存放在ibdata1.log
现在一般都选用InnoDB，主要是MyISAM的全表锁，读写串行问题，并发效率锁表，效率低，MyISAM对于读写密集型应用一般是不会去选用的。
应用场景：
 

MyISAM不支持事务处理等高级功能，但它提供高速存储和检索，以及全文搜索能力。如果应用中需要执行大量的SELECT查询，那么MyISAM是更好的选择。
InnoDB用于需要事务处理的应用程序，包括ACID事务支持。如果应用中需要执行大量的INSERT或UPDATE操作，则应该使用InnoDB，这样可以提高多用户并发操作的性能。
 
"""
