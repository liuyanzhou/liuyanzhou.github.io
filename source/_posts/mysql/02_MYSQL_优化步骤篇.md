---
title: 02_MYSQL_优化步骤篇
date: 2020-06-07 08:45:11
categories: mysql
img: /medias/imges/mysql/mysql.jpg
top: false
summary: 02_MYSQL_优化步骤篇
tags: 
 - mysql
---

#### 一、为何要学习SQL优化

在应用的的开发过程中，由于初期数据量小，开发人员写 SQL 语句时更重视功能上的实现，但是当应用系统正式 

上线后，随着生产数据量的急剧增长，很多 SQL 语句开始逐渐显露出性能问题，对生产的影响也越来越大，此时 

这些有问题的 SQL 语句就成为整个系统性能的瓶颈，因此我们必须要对它们进行优化，本章将详细介绍在 MySQL 

中优化 SQL 语句的方法。 

#### 二、SQL优化步骤

##### 2.1 查看SQL执行频率 

MYSQL 客户端连接成功后,通过 **SHOW [session| global] **status 命令可以提供服务器状态信息，show [seesion | global] status 可以根据需要加上参数`session`或`global`来显示session 级（当前连接）的统计结果和global级统计结果（自数据库上次启动至今）的统计结果,默认使用参数是"session"

下面命令显示了当前session中都有统计参数的值

```sql
show status like 'Com_______';
```

![SHOW STATUS](/medias/imges/mysql/sqlyouhua/1.jpg)

```sql
show status like 'Innodb_rows_%';
```

![SHOW STATUS](/medias/imges/mysql/sqlyouhua/2.jpg)

Com_xxx 表示每个 xxx 语句执行的次数，我们通常比较关心的是以下几个统计参数。

|         参数         |                           含义                            |
| :------------------: | :-------------------------------------------------------: |
|      Com_select      |          执行 select 操作的次数，一次查询值累加1          |
|      Com_insert      | 执行 INSERT 操作的次数，对于批量插入的INSERT操作，只累加1 |
|      Com_update      |                  执行 UPDDATE 操作的次数                  |
|      Com_delete      |                  执行 DELETE 操作的次数                   |
|   Innodb_rows_read   |                   select 查询返回的行数                   |
| Innodb_rows_inserted |                执行 INSERT 操作插入的行数                 |
|  Innodb_rows_delete  |                执行 DELETE 操作删除的行数                 |
|     Connections      |                试图连接 MYSQL 服务器的次数                |
|        Uptime        |                      服务器工作时间                       |
|     Slow_queries     |                       慢查询的次数                        |

> Com_***  ：这参数对于所有存储引擎的表操作都会进行累计
>
> Innodb_*** : 这几个参数只是针对InnoDB 存储引擎的, 累加的算法略有不同
>
> 小编默默说一句：**步骤一只是参考，在实际中并无发挥大的作用**

##### 2.2定位低效率执行SQL

慢查询日志定位:通过慢查询日志定位那些执行效率较低的 SQL 语句，用 --`log-slow-queries[=file_name]` 选项启动时，mysqld 写一个包含所有执行时间超过 `long_query_time`秒的 SQL 语句的日志文件.

* 慢查询日志默认是`关闭`的，可以通过两个参数来控制慢查询日志，配置文件在`Window`系统是`my.ini`，而`Linux` 系统是`my.cnf`

  ```ini
  # 该参数用来控制慢查询日志是否开启， 可取值： 1 和 0 ， 1 代表开启， 0 代表关闭
  slow_query_log=1
  # 该参数用来指定慢查询日志的文件名
  slow_query_log_file=slow_query.log
  # 该选项用来配置查询的时间限制， 超过这个时间将认为值慢查询， 将需要进行日志记录， 默认10s
  long_query_time=10
  ```

* 当语句设置超过查询时间限制，就会在MYSQL 安装目录下 `data`文件下生产一个`slow.query.log`文件，在浏览器打开为

  ![慢查询日志](/medias/imges/mysql/sqlyouhua/3.jpg)

  > 步骤二是优化的最关键的一步，他能够让你定位到影响系统效率的`SQL`语句

##### 2.3 explain 分析执行计划

通过以上步骤查询到效率低的 `SQL` 语句后，可以通过 `explain`命令获取`MYSQL` 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接顺序

```sql
explain  select * from tb_item where id = 1;
```

![EXPLAIN](/medias/imges/mysql/sqlyouhua/4.jpg)

```sql
explain select * from tb_item where title = '阿尔卡特 (OT-979) 冰川白 联通3G手机3';
```

![EXPLAIN](/medias/imges/mysql/sqlyouhua/5.jpg)

各字段代表的含义

|     字段      |                             含义                             |
| :-----------: | :----------------------------------------------------------: |
|      Id       | select 查询的序列号，是一组数字，表述的是查询中执行select子句或者是操作表的顺序 |
|  select_type  | 表述 SELECT 的类型，常见的取值有 `SIMPLE` (简单表，即不适用表连接或者子查询)、`PRIMARY`（主查询，即外层的查询）、`UNION`（UNION中的第二个或者后面的查询语句）、`SUBQUERY`（子查询中的第一个 SELECT）等 |
|     table     |                        输出结果集的表                        |
|     type      | 表述表的连接类型，性能由好到差的连接类型为（`system` > `const` > `eq_ref` > `ref` > `ref_or_null` >` index_merge` >` index_subquery` > `range` >` index` >` all`） |
| possible_keys |                  表示查询时，可能使用的索引                  |
|      key      |                      表示实际使用的索引                      |
|    key_len    |                        索引字段的长度                        |
|     rows      |                         扫描行的数量                         |
|     extra     |                     执行情况的说明和描述                     |

环境准备

![环境准备](/medias/imges/mysql/sqlyouhua/6.jpg)

```sql
CREATE TABLE `t_role` (
`id` varchar(32) NOT NULL,
`role_name` varchar(255) DEFAULT NULL,
`role_code` varchar(255) DEFAULT NULL,
`description` varchar(255) DEFAULT NULL,
PRIMARY KEY (`id`),
UNIQUE KEY `unique_role_name` (`role_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `t_user` (
`id` varchar(32) NOT NULL,
`username` varchar(45) NOT NULL,
`password` varchar(96) NOT NULL,
`name` varchar(45) NOT NULL,
PRIMARY KEY (`id`),
UNIQUE KEY `unique_user_username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `user_role` (
`id` int(11) NOT NULL auto_increment ,
`user_id` varchar(32) DEFAULT NULL,
`role_id` varchar(32) DEFAULT NULL,
PRIMARY KEY (`id`),
KEY `fk_ur_user_id` (`user_id`),
KEY `fk_ur_role_id` (`role_id`),
CONSTRAINT `fk_ur_role_id` FOREIGN KEY (`role_id`) REFERENCES `t_role` (`id`) ON
DELETE NO ACTION ON UPDATE NO ACTION,
CONSTRAINT `fk_ur_user_id` FOREIGN KEY (`user_id`) REFERENCES `t_user` (`id`) ON
DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert into `t_user` (`id`, `username`, `password`, `name`)
values('1','super','$2a$10$TJ4TmCdK.X4wv/tCqHW14.w70U3CC33CeVncD3SLmyMXMknstqKRe','
超级管理员');
insert into `t_user` (`id`, `username`, `password`, `name`)
values('2','admin','$2a$10$TJ4TmCdK.X4wv/tCqHW14.w70U3CC33CeVncD3SLmyMXMknstqKRe','
系统管理员');
insert into `t_user` (`id`, `username`, `password`, `name`)
values('3','itcast','$2a$10$8qmaHgUFUAmPR5pOuWhYWOr291WJYjHelUlYn07k5ELF8ZCrW0Cui',
'test02');
insert into `t_user` (`id`, `username`, `password`, `name`)
values('4','stu1','$2a$10$pLtt2KDAFpwTWLjNsmTEi.oU1yOZyIn9XkziK/y/spH5rftCpUMZa','学
生1');
insert into `t_user` (`id`, `username`, `password`, `name`)
values('5','stu2','$2a$10$nxPKkYSez7uz2YQYUnwhR.z57km3yqKn3Hr/p1FR6ZKgc18u.Tvqm','学
生2');
insert into `t_user` (`id`, `username`, `password`, `name`)
values('6','t1','$2a$10$TJ4TmCdK.X4wv/tCqHW14.w70U3CC33CeVncD3SLmyMXMknstqKRe','老师
1');
INSERT INTO `t_role` (`id`, `role_name`, `role_code`, `description`) VALUES('5','学
生','student','学生');
INSERT INTO `t_role` (`id`, `role_name`, `role_code`, `description`) VALUES('7','老
师','teacher','老师');
INSERT INTO `t_role` (`id`, `role_name`, `role_code`, `description`) VALUES('8','教
学管理员','teachmanager','教学管理员');
INSERT INTO `t_role` (`id`, `role_name`, `role_code`, `description`) VALUES('9','管
理员','admin','管理员');
INSERT INTO `t_role` (`id`, `role_name`, `role_code`, `description`) VALUES('10','超
级管理员','super','超级管理员');
INSERT INTO user_role(id,user_id,role_id) VALUES(NULL, '1', '5'),(NULL, '1', '7'),
(NULL, '2', '8'),(NULL, '3', '9'),(NULL, '4', '8'),(NULL, '5', '10') ;
```

###### **2.3.1 explain 之 id**

`id` 字段是 select 查询的序列号 ， 是一组数字， 表示的是查询中执行select子句或者是操作表的顺序，id 情况有三种

1） id 相同表示加载表的顺序是从上到下。

```sql
explain select * from t_role r, t_user u, user_role ur where r.id = ur.role_id and u.id = ur.user_id ;
```

![explain 之 id](/medias/imges/mysql/sqlyouhua/7.jpg)

2） id 不同id值越大，优先级越高，越先被执行。

```sql
EXPLAIN SELECT * FROM t_role WHERE id = (SELECT role_id FROM user_role WHERE user_id =(SELECT id FROM t_user WHERE username = 'stu1'))
```

![explain 之 id](/medias/imges/mysql/sqlyouhua/8.jpg)

3） id 有相同，也有不同，同时存在。id相同的可以认为是一组，从上往下顺序执行；在所有的组中，id的值越大，优先级越高，越先执行。

```sql
EXPLAIN SELECT * FROM t_role r , (SELECT * FROM user_role ur WHERE ur.`user_id` ='2') a WHERE r.id = a.role_id ;
```

![explain 之 id](/medias/imges/mysql/sqlyouhua/9.jpg)

###### **2.3.2 explain 之 select_type**

常见的取值，如下表所示

| select_type  |                             含义                             |
| :----------: | :----------------------------------------------------------: |
|    SIMPLE    |       简单的 select 查询，查询中不包含子查询或者 UNION       |
|   PRIMARY    |     查询中若包含如何复杂的子查询，最外层查询标记为该标识     |
|   SUBQUERY   |            在 SELECT 或 WHERE 列表中包含了子查询             |
|   DERIVED    | 在FROM 列表中包含的子查询，被标记为 DERIVED(衍生) MYSQL 会递归执行这些子查询，把结果放在临时表中 |
|    UNION     | 若第二个SELECT 出现在UNION之后，则标记为UNION；若UNION 包含在FROM 子句，外层SELECT将被标记为： DERIVED |
| UNION RESULT |                  从UNION 表获取结果的SELECT                  |

```cmd
# SIMPLE
explain select * from t_user;
# PRIMARY && SUBQUERY [t_user -> PRIMARY ,  user_role -> SUBQUERY ]
explain select * from t_user where id = (select id from user_role where role_id = '9');
# DERIVED [t_user -> DERIVED]
explain select a.* from (select * from t_user where id in ('1','2')) a;
# UNION &&  UNION RESULT [ 第一个`t_user` -> PRIMARY, 第2个`t_user` -> union, 联合起来表 -> UNION RESULT ]
explain select * from t_user where id = '1' union select * from  t_user whereid ='2';

```

> 值得一提的是 这些 查询类型效率是按顺序逐步`降低`的

###### **2.3.3 explain 之 table**

展示这一行的数据是关于哪一张表的

###### **2.3.4 explain 之 type**

type 显示的是访问类型，**是较为重要的一个指标**，可取值为:

|  type  |                             含义                             |
| :----: | :----------------------------------------------------------: |
|  NULL  |            MYSQL不访问任何表，索引，直接返回结果             |
| system | 表只有一行记录(等于系统表)，这是`const`类型的特例，一般不会出现 |
| const  | 表示通过索引一次就找到了，`const`  用于 `primary key` 或者`unique`索引查找。因为只匹配一行数据，所以很快。如将主键置于`where`列表中，MYSQL 就能将该查询转换为一个`常量`,const会将`主键`或`唯一索引`的所有部分与常量值进行比较 |
| eq_ref | 类型`ref`区别在于使用的是唯一索引，使用主键的关联查询，关联查询的记录只有一条。常见于主键或唯一索引扫描 |
|  ref   | 非唯一性索引扫描，返回匹配某个单独值得所有行。本质上也是一种索引访问，返回所有匹配某个单独值的所有行（多个） |
| range  | 只检索给定返回的行，使用一个索引来选择行。where之后出现 betwwen ， < , > ,in 等操作。 |
| index  | index 与 ALL 区别为 index 类型只是遍历了索引树，通常比ALL快， ALL 是遍历数据文件 |
|  all   |                   将遍历全表以找到匹配的行                   |

模拟：

```sql
-- NULL [不访问任何表]
explain select nwo();
--system [表只有一行记录]
explain select * from (select * from t_user where id = '1') a;
-- const [primary key 或者unique索引查找]
-- 主键
explain select * from t_user where id = '1'; 
-- 唯一索引
explain select * from t_user where username = 'stu1';
-- eq_ref [ 主键或唯一索引 关联查询的记录只有一条 ]
explain select * from t_user u,t_role r where u.id = r.id;
-- ref 非唯一性索引扫描
-- name 是普通索引类型
explain select * from t_user where name= 'a';
-- range [范围查询]
explain select * from tb_book where id BETWEEN 2 AND 4;
-- index [只遍历索引树]
explain select id from t_user; 
-- all [扫描全表]
explain select * from t_user;
```

结果值从最好到最坏是:

> system > const > eq_ref > ref > range > index > all 
>
> **一般来说， 我们需要保证查询至少达到 range 级别， 最好达到ref  **

###### **2.3.5 explain 之 key** 

> possible_keys : 显示可能应用在这张表的索引，一个或多个
>
> key ： 实际使用的索引 ，如果为 NULL ,则没有使用索引
>
> key_len : 表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好

###### **2.3.6 explain 之 ref**

> 列与索引的比较，表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值
>
> const 常量优化程度高

###### **2.3.7 explain 之 rows** 

扫描行的数量

###### **2.3.8 explain 之 extra**

其他的额外的执行计划信息，在该列展示

> 我们要避免 `using filesort` 与 `using temporary`,尽量往`using index`优化，而出现 `using filesort` 与`using temporary`原因都是未建立合理的索引导致的，下面`using index` 说的覆盖索引就是根据索引查找已经建立的索引字段

|      extra      |                             含义                             |
| :-------------: | :----------------------------------------------------------: |
| using filesort  | 说明mysql会对数据一个外部的索引排序，而不是按照表内的索引的顺序进行读取，称为`文件排序`,效率低 |
| using temporary | 使用了临时表保存中间结果,MYSQL 在对查询结果排序时使用临时表，常见于 `order by` 和 `group by` ；效率低 |
|   using index   | 表示相应的select操作使用了覆盖索引，避免访问表的数据行，效率不错 |

```cmake
# using filesort [password并为建立索引就会出现 `using filesort`]
explain select * from t_user order by password;
# using temporary [password并为建立索引就会出现 ` using temporary;using filesort`]
explain select * from t_user group  by password;
# using index [ name建立了索引 ] 
explain select name from t_user order by name
```
> 小编寄语：掌握了定位效率低的`SQL`语句，在进行`explain`工具分析，找出影响效率的原因，之后根据优化技巧进行高效优化，最有效的优化就是根据`索引`优化`SQL`，但有一些情况下建立的索引会出现失效的情况，小编将失效情况写入`03_MYSQL_索引失效篇`

