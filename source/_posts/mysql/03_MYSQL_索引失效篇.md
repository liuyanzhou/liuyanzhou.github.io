---
title: 03_MYSQL_索引失效篇
date: 2020-06-07 11:51:59
categories: mysql
img: /medias/imges/mysql/mysql.jpg
top: false
summary: 03_MYSQL_索引失效篇
tags: 
 - mysql
---

#### 索引失效篇

> 索引是数据库优化最常用也是最重要的手段之一, 通过索引通常可以帮助用户解决大多数的MySQL的性能优化问
>
> 题。 本章主要讲述什么情况下索引会出现无效的情况，并且如何解决

#### 一、构建环境

```sql
create table `tb_seller` (
 `sellerid` varchar (100),
 `name` varchar (100),
 `nickname` varchar (50),
 `password` varchar (60),
 `status` varchar (1),
 `address` varchar (100),
 `createtime` datetime,
  primary key(`sellerid`)
)engine=innodb default charset=utf8mb4;

insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('alibaba','阿里巴巴','阿里小
店','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('baidu','百度科技有限公司','百度小
店','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('huawei','华为科技有限公司','华为小
店','e10adc3949ba59abbe56e057f20f883e','0','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('itcast','传智播客教育科技有限公司','传智播
客','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00');
insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('itheima','黑马程序员','黑马程序
员','e10adc3949ba59abbe56e057f20f883e','0','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('luoji','罗技科技有限公司','罗技小
店','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('oppo','OPPO科技有限公司','OPPO官方旗舰
店','e10adc3949ba59abbe56e057f20f883e','0','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('ourpalm','掌趣科技股份有限公司','掌趣小
店','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('qiandu','千度科技','千度小
店','e10adc3949ba59abbe56e057f20f883e','2','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('sina','新浪科技有限公司','新浪官方旗舰
店','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('xiaomi','小米科技','小米官方旗舰
店','e10adc3949ba59abbe56e057f20f883e','1','西安市','2088-01-01 12:00:00'); insert into `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`,
`address`, `createtime`) values('yijia','宜家家居','宜家家居旗舰
店','e10adc3949ba59abbe56e057f20f883e','1','北京市','2088-01-01 12:00:00'); 

-- 创建 name \ status \ address 复合索引
create index idx_seller_name_sta_addr on tb_seller(name,status,address);
```

#### 二、避免索引失效策略

##### 1). 全值匹配,对索引中所有列都指定具体值 

该情况下，索引生效，执行效率高

```sql
explain select * from tb_seller where name='小米科技' and status='1' and address='北京 市'\G;
```

![索引生效](/medias/imges/mysql/syoyinsixiao/1.jpg)

##### 2). 最左前缀法则 

如果索引是复合索引，要遵守最左前缀法则。指的是查询从索引的最左前列开始，并且不跳过索引中的列。 

匹配最左前缀法则，走索引： 

![最左前缀法则](/medias/imges/mysql/syoyinsixiao/2.jpg)

违法最左前缀法则 ， 索引失效：[**这里未包含最左的索引列，故不走索引**]

![最左前缀法则](/medias/imges/mysql/syoyinsixiao/3.jpg)

如果符合最左法则，但是出现跳跃某一列，只有最左列索引生效：[**跳跃索引中间列，只走最侧索引**]

![最左前缀法则](/medias/imges/mysql/syoyinsixiao/4.jpg)

##### 3). 范围查询右边的列，不能使用索引 。

![范围查询](/medias/imges/mysql/syoyinsixiao/5.jpg)

> 根据前面的两个字段`name ， status` 查询是走索引的， 但是最后一个条件`address` 没有用到索引。

##### 4). 不要在索引列上进行运算操作， 索引将失效。

![运算操作](/medias/imges/mysql/syoyinsixiao/6.jpg)

##### 5). 字符串不加单引号，造成索引失效。

![字符串不加单引号](/medias/imges/mysql/syoyinsixiao/7.jpg)

> 其实字符串不加单引号，造成索引失效是因为该列是字符串类型限制，当不加单引号的时候，在`MYSQL`解析语句时查询优化器内部会自动的进行类型转换,也就是进行了运算操作，故索引失效

##### 6). 尽量使用覆盖索引，避免select *

> **尽量使用覆盖索引（只访问索引的查询（索引列完全包含查询列）），减少select * 。** 
>
> ![尽量使用覆盖索引](/medias/imges/mysql/syoyinsixiao/8.jpg)



> **如果查询列，超出索引列，也会降低性能。**
>
> ![查询列超出索引列](/medias/imges/mysql/syoyinsixiao/9.jpg)

> TIP :
>
> ​	using index ：使用覆盖索引的时候就会出现
>
> ​	using where：在查找使用索引的情况下，需要回表去查询所需的数据
>
> ​	using index condition：查找使用了索引，但是需要回表查询数据
>
> ​	using index ; using where：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据
>
> 注意：最好优化到 `using index ; using where`，通过索引查询到符合的数据，即刻返回，不需要回表查找，这出现于查询列是覆盖索引情况下

##### 7). 用or分割开的条件， 如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。 

示例，name字段是索引列 ， 而createtime不是索引列，中间是or进行连接是不走索引的 ：

```sql
explain select * from tb_seller where name='黑马程序员' or createtime = '2088-01-01 12:00:00'\G;
```

![or分割开的条件](/medias/imges/mysql/syoyinsixiao/10.jpg)

##### 8). 以%开头的Like模糊查询，索引失效。 

如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效。 

![%开头的Like模糊查询](/medias/imges/mysql/syoyinsixiao/11.jpg)

> 解决方案：**通过覆盖索引来解决**

![解决方案](/medias/imges/mysql/syoyinsixiao/12.jpg)

##### 9). 如果MySQL评估使用索引比全表更慢，则不使用索引。

![索引比全表更慢](/medias/imges/mysql/syoyinsixiao/13.jpg)

数据表说明

![索引比全表更慢](/medias/imges/mysql/syoyinsixiao/14.jpg)

> 由表可知： `address`列中几乎都为`北京市`,而`西安市`只有一个，当以`西安市`查询时表就走索引，当以`北京市`查询时，MYSQL发现`北京市`占比太大，评估索引比全表更慢后，不走索引，走全表扫描

##### 10). is NULL ， is NOT NULL 有时索引失效。

![索引失效](/medias/imges/mysql/syoyinsixiao/15.jpg)

> 这里的索引有时失效的原因和第9点的一样，当查询列`null`占比少，用`is null `便走索引,当查询`null`占比多，用`is not null `走索引，相反，`null`占比多,用`is null `查询索引变失效,`null`占比少,用`is not null`查询索引失效。

##### 11). in 走索引， not in 索引失效。

![索引失效](/medias/imges/mysql/syoyinsixiao/16.jpg)

##### 12). 单列索引和复合索引。 

尽量使用复合索引，而少使用单列索引 。 

创建复合索引 ：

```sql
create index idx_name_sta_address on tb_seller(name, status, address);
-- 就相当于创建了三个索引 ：
 -- name
 -- name + status
 -- name + status + address
```

创建单列索引 :

```sql
create index idx_seller_name on tb_seller(name);
create index idx_seller_status on tb_seller(status);
create index idx_seller_address on tb_seller(address);
```

> 如果都用单列索引，数据库会选择一个最优的索引（辨识度最高索引）来使用，并不会使用全部索引 ，而多个条件查询时，复合索引的效率高于单列索引。

#### 三、小结

> 这里提出了12种情况下索引失效，并不用死记硬背，我们只要在写sql语句时，注意这些方面，在根据写好的语句进行`explain`分析，便能够有效归避索引失效的情况。奥里给           💪



