---
title: 04_MUSQL_优化实例篇
date: 2020-06-07 16:35:04
categories: mysql
img: /medias/imges/mysql/mysql.jpg
top: false
summary: 04_MUSQL_优化实例篇
tags: 
 - mysql
---

#### MYSQL_优化实力篇

> 本篇小编叙述一些语句的优化技巧，全干货😎，做好笔记！！！🖍

##### 一、优化insert语句

当进行数据的insert操作的时候，可以考虑采用以下几种优化方案。

* 如果需要同时对一张表插入很多行数据时，应该尽量使用多个值表的insert语句，这种方式将大大的缩减客户
  端与数据库之间的连接、关闭等消耗。使得效率比分开执行的单个insert语句快。
  示例， 原始方式为：

  ```sql
  insert into tb_test values(1,'Tom');
  insert into tb_test values(2,'Cat');
  insert into tb_test values(3,'Jerry');
  ```

  优化后的方案为 ：

  ```sql
  insert into tb_test values(1,'Tom'),(1 2,'Cat')，(3,'Jerry');
  ```

* 在事务中进行数据插入。

  ```sql
  start transaction;
  insert into tb_test values(1,'Tom');
  insert into tb_test values(2,'Cat');
  insert into tb_test values(3,'Jerry');
  commit;
  ```

* 数据有序插入

  原始:

  ```sql
  insert into tb_test values(4,'Tim');
  insert into tb_test values(1,'Tom');
  insert into tb_test values(3,'Jerry');
  insert into tb_test values(5,'Rose');
  insert into tb_test values(2,'Cat');
  ```

  优化后:

  ```sql
  insert into tb_test values(1,'Tom');
  insert into tb_test values(2,'Cat');
  insert into tb_test values(3,'Jerry');
  insert into tb_test values(4,'Tim');
  insert into tb_test values(5,'Rose');
  ```

##### 二、优化order by语句

环境准备：

```sql
CREATE TABLE `emp` (
 `id` int(11) NOT NULL AUTO_INCREMENT,
 `name` varchar(100) NOT NULL,
 `age` int(3) NOT NULL,
 `salary` int(11) DEFAULT NULL,
 PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

insert into `emp` (`id`, `name`, `age`, `salary`) values('1','Tom','25','2300'); 
insert into `emp` (`id`, `name`, `age`, `salary`) values('2','Jerry','30','3500'); 
insert into `emp` (`id`, `name`, `age`, `salary`) values('3','Luci','25','2800');
insert into `emp` (`id`, `name`, `age`, `salary`) values('4','Jay','36','3500'); 
insert into `emp` (`id`, `name`, `age`, `salary`) values('5','Tom2','21','2200'); 
insert into `emp` (`id`, `name`, `age`, `salary`) values('6','Jerry2','31','3300');
insert into `emp` (`id`, `name`, `age`, `salary`) values('7','Luci2','26','2700'); 
insert into `emp` (`id`, `name`, `age`, `salary`) values('8','Jay2','33','3500');
insert into `emp` (`id`, `name`, `age`, `salary`) values('9','Tom3','23','2400');
insert into `emp` (`id`, `name`, `age`, `salary`)values('10','Jerry3','32','3100'); 
insert into `emp` (`id`, `name`, `age`, `salary`) values('11','Luci3','26','2900');
insert into `emp` (`id`, `name`, `age`, `salary`) values('12','Jay3','37','4500'); 

create index idx_emp_age_salary on emp(age,salary);
```

**排序方式**

1). 第一种是通过对返回数据进行排序，也就是通常说的 fifilesort 排序，所有不是通过索引直接返回排序结果的排序 都叫 ` FileSort `排序。

![fifilesort](/medias/imges/mysql/suoyinsili/1.jpg)

2). 第二种通过有序索引顺序扫描直接返回有序数据，这种情况即为 `using index`,不需要额外排序，操作效率高。

![index](/medias/imges/mysql/suoyinsili/2.jpg)

> 要优化到using index程度，就要将查询列进行覆盖索引 

3）多字段排序

![多字段排序](/medias/imges/mysql/suoyinsili/3.jpg)

> 了解了MySQL的排序方式，优化目标就清晰了：尽量减少额外的排序，通过索引直接返回有序数据。where 条件和Order by 使用相同的索引，并且Order By 的顺序和索引顺序相同， 并且Order by 的字段都是升序，或者都是降序。否则肯定需要额外的操作，这样就会出现`FileSort`。🎉

4) Filesort 的优化

通过创建合适的索引，能够减少 Filesort 的出现，但是在某些情况下，条件限制不能让Filesort消失，那就需要加
快 Filesort的排序操作。对于Filesort ， MySQL 有两种排序算法：

*  两次扫描算法 ：MySQL4.1 之前，使用该方式排序。首先根据条件取出排序字段和行指针信息，然后在排序区sort buffer 中排序，如果sort buffer不够，则在临时表 temporary table 中存储排序结果。完成排序之后，再根据行指针回表读取记录，该操作可能会导致大量随机I/O操作。
* 一次扫描算法：一次性取出满足条件的所有字段，然后在排序区 sort buffer 中排序后直接输出结果集。排序时内存开销较大，但是排序效率比两次扫描算法要高。

> MySQL 通过比较系统变量 `max_length_for_sort_data` 的大小和`Query`语句取出的字段总大小， 来判定是否那种排序算法，如果max_length_for_sort_data 更大，那么使用第二种优化之后的算法；否则使用第一种。

可以适当提高 sort_buffer_size 和 max_length_for_sort_data 系统变量，来增大排序区的大小，提高排序的效
率。

![Filesort 的优化](/medias/imges/mysql/suoyinsili/4.jpg)

##### 三、优化group by 语句

由于GROUP BY 实际上也同样会进行排序操作，而且与ORDER BY 相比，GROUP BY 主要只是多了排序之后的分
组操作。当然，如果在分组的时候还使用了其他的一些聚合函数，那么还需要一些聚合函数的计算。所以，在
GROUP BY 的实现过程中，与 ORDER BY 一样也可以利用到索引。

如果查询包含 group by 但是用户想要避免排序结果的消耗， 则可以执行order by null 禁止排序。如下 ：

```sql
drop index idx_emp_age_salary on emp;
explain select age,count(*) from emp group by age;
```

![优化group by 语句](/medias/imges/mysql/suoyinsili/5.jpg)

优化后:

```sql
explain select age,count(*) from emp group by age order by null;
```

![优化group by 语句](/medias/imges/mysql/suoyinsili/6.jpg)

> 从上面的例子可以看出，第一个SQL语句需要进行"filesort"，而第二个SQL由于order by null 不需要进行
> "filesort"， 而上文提过Filesort往往非常耗费时间。

进一步优化:`创建索引`

```sql
create  index idx_emp_age_salary on emp(age,salary)；
```

再一次查询`group by` 便会根据索引来分组，提高效率

##### 四、优化嵌套查询

Mysql4.1版本之后，开始支持SQL的子查询。这个技术可以使用SELECT语句来创建一个单列的查询结果，然后把
这个结果作为过滤条件用在另一个查询中。使用子查询可以一次性的完成很多逻辑上需要多个步骤才能完成的SQL
操作，同时也可以避免事务或者表锁死，并且写起来也很容易。但是，有些情况下，子查询是可以被更高效的连接
（JOIN）替代

示例 ，查找有角色的所有的用户信息 :

```sql
explain select * from t_user where id in (select user_id from user_role );
```

执行计划为 :

![优化嵌套查询](/medias/imges/mysql/suoyinsili/7.jpg)

优化后 :

```sql
explain select * from t_user u , user_role ur where u.id = ur.user_id;
```

![优化嵌套查询](/medias/imges/mysql/suoyinsili/8.jpg)

> 连接(Join)查询之所以更有效率一些 ，是因为MySQL不需要在内存中创建临时表来完成这个逻辑上需要两个步骤的查询工作。

##### 五、优化OR条件

对于包含OR的查询子句，如果要利用索引，则OR之间的每个条件列都必须用到索引 ， 而且不能使用到复合索
引； 如果没有索引，则应该考虑增加索引。

获取 emp 表中的所有的索引 ：

![优化OR条件](/medias/imges/mysql/suoyinsili/9.jpg)

示例 ：

```sql
explain select * from 1 emp where id = 1 or age = 30;
```

![优化OR条件](/medias/imges/mysql/suoyinsili/10.jpg)

![优化OR条件](/medias/imges/mysql/suoyinsili/11.jpg)

建议使用 union 替换 or ：

![优化OR条件](/medias/imges/mysql/suoyinsili/12.jpg)

我们来比较下重要指标，发现主要差别是 type 和 ref 这两项
type 显示的是访问类型，是较为重要的一个指标，结果值从好到坏依次是：

> system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL
>
> UNION 语句的 type 值为 ref，OR 语句的 type 值为 range，可以看到这是一个很明显的差距
> UNION 语句的 ref 值为 const，OR 语句的 type 值为 null，const 表示是常量值引用，非常快
> 这两项的差距就说明了 UNION 要优于 OR 。

##### 六、优化分页查询

一般分页查询时，通过创建覆盖索引能够比较好地提高性能。一个常见又非常头疼的问题就是 limit 2000000,10 ，此时需要MySQL排序前2000010 记录，仅仅返回2000000 - 2000010 的记录，其他记录丢弃，查询排序的代价非常大 。

![优化分页查询](/medias/imges/mysql/suoyinsili/13.jpg)

1）**优化思路一：**在索引上完成排序分页操作，最后根据主键关联回原表查询所需要的其他列内容。
![优化思路一](/medias/imges/mysql/suoyinsili/14.jpg)

2）**优化思路二：** 该方案适用于主键自增的表，可以把Limit 查询转换成某个位置的查询 。
![优化思路二](/medias/imges/mysql/suoyinsili/15.jpg)

> 优化思路二 只适合不断层的主键自增的表，如果断层的话数据会不准确，`弊端很大`