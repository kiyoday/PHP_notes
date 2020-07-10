

## 查找分析查询速度慢的原因

1. **记录慢查询**日志
   分析查询日志，不要直接打开慢查询日志进行分析，这样比较浪费
   时间和精力，可以使用`pt-query-digest`工具进行分析

2. 使用 **show profile**
   `set profiling=1`

   开启，服务器上执行的所有语句会检测消耗的时间，存到`临时表`中

```mysql
set profiling=1;   //开启 profile
show profiles
show profile for query临时表ID
```

- demo

```mysql
mysql > set profiling=1;s
mysql > select from a;
mysql> show profiles;
```

1. 使用 `show status`
   `show status`会返回一些计数器， `show global status`查看服务器级别的所有计数有时根据这些计数，可以猜测出哪些操作代价较高或者消耗时间多

2. 使用 `show processlist`
   观察是否有大量线程处于不正常

3. 使用 `explain`/`desc`
   分析**单条SQL**语句

   ```mysql
   mysql> explain select * from fq_order\G;
   ```

## 优化查询过程中的数据访问

- 访问`数据太多`导致查询性能下降
- 确定应用程序是否在检索大量超过需要的数据，可能是太多行或列
- 确认 MYSQL服务器是否在分析大量不必要的数据行
- 改变数据库和表的结构，修改数据表范式（反范式设计）：
  - 使用`冗余`，把第二张表数据复制到第一张表中，空间换时间
- 重写SQL语句，让优化器可以以更优的方式执行查询

### 避免使用如下SQL语句

- 查询不需要的记录，使用` limit`解决
- 多表关联返回全部列，指定`列名` 如:A.id，A.name，B.age
- 总是取出全部列， `SELECT*`会让优化器无法完成索引覆盖扫描的优化
- 重复查询`相同`的数据，可以`缓存`数据，下次直接读取缓存

### 是否在扫描额外的记录

使用 explain来进行分析，如果发现查询需要扫描大量的数据但只返回少数的行，可以通过如下技巧去优化:
使用`索引`覆盖扫描，把所有用的列都放到索引中，这样存储引擎不需要回表获取对应行就可以返回结果



## 优化长难的查询语句

**一个复杂查询好与多个简单查询?**

- MySQL内部每秒能扫描内存中上百万行数据，相比之下，响应数据给客户端就要慢得多
- 使用尽可能少的查询是好的，但是有时将一个大的查询分解为多个小的查询是很有必要

### 切分查询

​	将一个大的查询分为多个小的相同的查询

​	一次性删除1000万的数据要比一次删除1万，暂停一会的方案更加损耗服务器开销

### 分解关联查询

可以将一条`关联语句`分解成多条SQL来执行

- 让缓存的效率更高
- 执行单个查询可以减少锁的竞争
- 在应用层做关联可以更容易对数据库进行拆分
- 查询效率会有大幅提升
- 较少冗余记录的查询

## 优化特定类型的查询语句

### 优化 `count()`查询

- `count(*)`中的`*`会忽略所有的列，直接统计所有列数，因此**不要使用 count(列名)**

- MYISAM中，没有任何 WHERE条件的`count(*)`非常快，当有 WHERE条件， 

  MYISAM的 count统计不一定比其他表引擎快

- 可以使用 explain查询近似值，用`近似值`替代 count()

- 增加`汇总表`

- 使用`缓存`

### 优化`关联查询`

- 确定`ON`或者 `USING`子句的列上`有索引`
- 确保` GROUP BY`和 `ORDER BY`中只有`一个`表中的列，这样 MYSQL才有可能使用索引

### 优化`子查询`

​		尽可能使用`关联查询`来替代

### 优化 `GROUP BY`和 `DISTINCT`

- 这两种查询均可使用`索引`来优化，是最有效的优化方法
- 关联查询中，使用标识列进行分组的效率会更高
- 如果不需要 ORDER BY，进行 GROUP BY 时使用 `ORDER BY NULL`， MYSQL不会再进行文件排序
- WITH ROLLUP超级聚合，可以挪到应用程序处理

> 分类聚合后的结果进行汇总 [参考用法简书](https://www.jianshu.com/p/5d2f700b0a31)

### 优化LIMIT分页

- LIMIT偏移量大的时候，查询效率较低
- 可以记录上次查询的最大ID ，下次查询时直接根据该ID来查询

### 优化UNION查询

UNION ALL的效率高于 UNION

## 如何获取有性能问题的SQL

- 通过用户反馈获取存在性能问题的SQL

- 通过慢查日志获取存在性能问题的SQL
- 实时获取存在性能问题的SQL

### 使用慢查询日志获取有性能问题的SQL

- `磁盘IO`和

- 存储日志所需要的`磁盘空间`

| 配置项                          | 功能                              |
| ------------------------------- | --------------------------------- |
| `slow_query_log`                | 启动停止记录慢查日志              |
| `slow_query_log_file`           | 指定慢查日志的存储路径及文件      |
| `long_query_time`               | 指定记录慢查日志SQL执行时间的伐值 |
| `log_queries_not_using_indexes` | 是否记录未使用索引的SQL           |

#### 开启慢查询日志

```mysql
set global slow_query_log  = on 
set global long_query_time  = on 
```

#### 慢查日志中记录的内容

![image-20200708113216954](C:\Users\12605\Desktop\PHP_notes\.img\image-20200708113216954.png)

#### 常用的慢查日志分析工具`(mysqldumpslow)`

> 汇总除查询条件外其它完全相同的SQL ,
> 并将分析结果按照参数中所指定的顺序输出。

![image-20200708114434811](C:\Users\12605\Desktop\PHP_notes\.img\image-20200708114434811.png)

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200708114453928.png" alt="image-20200708114453928" style="zoom:80%;float:left" />



#### 慢查询日志实例

![image-20200708114643166](C:\Users\12605\Desktop\PHP_notes\.img\image-20200708114643166.png)

#### 常用的慢查日志分析工具`(pt-query-digest)`

指定从服务器来进行生成慢查询日志优化

![image-20200708114839005](C:\Users\12605\Desktop\PHP_notes\.img\image-20200708114839005.png)



### 实时获取性能问题SQL

![image-20200708115211398](C:\Users\12605\Desktop\PHP_notes\.img\image-20200708115211398.png)

```mysql
SELECT `id`, `user`,`hos` ,`DB`,`command`, `time` ,`state`,`info`
FROM information_schema.PROCESSLIST
WHERE TIME> = 60
```



## SQL的解析预处理及生成执行计划

### 查询速度为什么会慢

- 客户端发送SQL请求给服务器
- 服务器检查是否可以在查询缓存中命中该SQL
- 服务器端进行SQL解析，预处理，再由优化器生成对应的执行计划
- 

#### 如何确定查询处理各个阶段所消耗的时间

## 特定SQL的查询优化