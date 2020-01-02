# SQL语言

> 3.4 关系数据库语言SQL’92
>
> 3.4.1 SQL概貌
>
> * SQL的基本概念与使用方式：表（基表/虚表），行，列
>
> 3.4.2 SQL数据定义功能
>
> 3.4.3 SQL数据操纵功能
>
> * SQL语言与关系代数的关系
> * 映像语句（select statement）的结构
> * select/from/where/group by/having/order by
> * 两个必须的子句: select/from
> * having子句的前面必须有group by子句
> * 基本查询功能：distinct谓词；LIKE，IS NULL谓词；多表联接查询；表的自联接查询；
> * 嵌套查询：IN，SOME/ANY/ALL，EXISTS等谓词；相关子查询与独立子查询；
> * 子查询的合并：UNION/INTERSECT/EXCEPT/ALL运算
> * UNION / INTERSECT / EXCEPT
> * UNION ALL / INTERSECT ALL / EXCEPT ALL
> * 统计查询（GROUP BY和HAVING）：统计与分组统计查询；空值与空集在统计函数中的处理方法
> * 复杂数据查询：
> * 统计查询
> * 分组统计查询（… GROUP BY）
> * 分组选择统计查询（… GROUP BY … HAVING …）
> * 关系代数中的除法运算功能在SQL中的表示方法
> * 多层嵌套的NOT EXISTS查询
> * 在FROM子句中嵌入子查询
> * 查询结果输出
> * 结果元组去重：distinct
> * 结果元组排序：order by
>
> 3.4.4 SQL的更新功能
>
> * 元组删除
> * 元组插入：常量元组的插入，带子查询的元组插入
> * 元组修改
>
> 3.4.5 视图
>
> * 视图概念，视图与基表的区别
> * 视图的创建（CREATE VIEW）与删除（DROP VIEW）
> * 视图的嵌套定义
> * 视图删除中的连锁反应
> * 可更新视图的判断准则
> * 视图的作用

## SQL概貌

关系模型和SQL对应：

* 关系：基表
* 关系子模式：视图\(虚表\)
* 属性：列
* 元组：行

SQL不区分大小写

一条命令以分号作为结束

## SQL 数据定义功能

| 符号 | 数据类型 |
| :--- | ---: |
| INT | 整数\(2147483647\) |
| SMALLINT | 短整数\(32767\) |
| DEC\(m,n\) | 十进制数\(精度为m,小数位为n的小数\) |
| FLOAT | 浮点数 |
| CHAR\(n\) | 定长字符串 |
| VARCHAR\(n\) | 变长字符串 |
| BIT\(n\) | 定长位串 |
| BIT VARYING\(n\) | 变长位串 |
| DATE | 日期\(年月日\) |
| TIME | 时间\(小时、分、秒\) |
| TIMESTAMP | 时间戳\(年月日小时分钟秒微秒\) |

时间戳的格式：yyyy-mm-dd-hh.mm.ss.nnnnnn

创建表格：

```sql
CREATE  TABLE  tablename (  
    colname  datatype  [ NOT NULL ]
    { , colname  datatype  [ NOT NULL ] }
) ;
```

对基表的属性进行增加/删除：

```sql
ALTER  TABLE  <基表名>  ADD  <列名>  <数据类型>;
ALTER  TABLE  <基表名>  DROP  <列名> ;
```

删除表格

```sql
DROP TABLE <基表名>;
```

## SQL数据操纵功能

映像语句（select statement）的结构：

* 目标子句
* 范围子句
* 条件子句
* 分组子句
* 分组查询语句
* 排序输出语句

```sql
SELECT  * | colname { , colname ... }
FROM  tablename { , tablename ... }
[ WHERE  search_condition ]
[ GROUP BY  colname { , colname ... }
[ HAVING  group_condition ] ]
[ ORDER BY  colname [ ASC | DESC ]
 { , colname [ ASC | DESC ] ... } ];
```

**having子句的前面必须有group by子句**

目标子句可以用'表名.属性名'来表明是哪一个表中的属性

可以用AS对目标子句的结果重命名

可以用**distinct**来消除结果中的重复元组

可以在FROM子句中对一个关系重命名：\ \

FROM子句中的部分是进行笛卡尔乘积进行合并

**BETWEEN..AND..** 谓词：包括了两端\(对应的有**NOT BETWEEN AND**\)

#### LIKE谓词：

`column [NOT] LIKE val1 [ ESCAPE val2 ]`

模版（pattern）：val1

* 下划线（\_）：可以匹配任意一个字符
* 百分号（%）：可以匹配任意一个字符串（包括长度为0的空字符串）
* 其它字符：只能匹配其自身

例如以A开头的学生姓名：`WHERE sn LIKE 'A%'`

转义指示字符：val2

* 紧跟在转义指示字符val2之后的‘\_’或‘%’（包括转义字符自身）不再是通配符，而是其自身

例如课程名中带有\_ 的：`WHERE cn LIKE '%A_%' ESCAPE 'A'`

**IS NULL** 与 **IS NOT NULL**；

在查询语句中可以使用**NOT**、**AND**、**OR**构造复杂的逻辑式

**嵌套查询**：

* \[NOT\] IN \(\)
* $\theta$ SOME/ANY/ALL \(\)
* \[NOT\] EXISTS\(\)

相关子查询:子查询中调用了外层查询，每次外层元组变量变化后都重新执行一次子查询，执行顺序从外到内

独立子查询: 子查询只执行一次，执行顺序从内到外

子查询之间的运算：

* 并：UNION\[ALL\]
* 交：INTERSECT\[ALL\]
* 差：EXCEPT\[ALL\]

统计查询：

* COUNT
  * COUNT\(\*\):集合中的元组个数
  * COUNT\(colname\)：在colname属性上取值非空的元组个数
  * COUNT\(distinct colname\)：在colname属性上非空且互不相同的元组个数
* SUM
* AVG
* MAX
* MIN

统计查询一般运用在SELECT语句中，如果需要使用条件可以利用子查询，举例如下

```sql
\\ 查询有两个或两个以上的客户订购过的商品的编号（pid）
SELECT p.pid
FROM     products  p
WHERE  2 <= ALL (SELECT count(distinct cid)
                  FROM      orders  o
                 WHERE  o.pid = p.pid )
```

**不等于的表示**：\&lt;&gt;

使用了GROUP BY 语句之后，SELECT语句对每个分组进行查询，每个分组返回一条结果语句

tip：在成绩类的判断中，常见的对于一个学生可以利用sno进行分组

**分组统计查询中，目标属性必须包括所有的分组属性**

在一些DBMS中，允许在FROM子句中嵌入子查询，举例如下：

```sql
\\每个供应商单笔销售最高金额的平均值
SELECT  avg ( t.x )
FROM     ( select  aid, max(dollars)  as  x
        from   orders
        group by  aid )   t  ;
```

关系代数中的除法运算功能在SQL中的表示方法

多层嵌套的NOT EXISTS查询

在FROM子句中嵌入子查询

查询结果输出

结果元组去重：distinct

结果元组排序：order by

映像语句的处理顺序:

* 合并FROM子句中的表（笛卡儿乘积）
* 利用WHERE子句中的条件进行元组选择，抛弃不满足WHERE条件的那些元组
* 根据GROUP BY子句对保留下来的元组进行分组
* 利用HAVING子句中的条件对分组后的元组集合（group）进行选择，抛弃不满足HAVING条件的那些元  组集合
* 根据SELECT子句进行统计计算，生成结果关系中的元组
* 根据ORDER BY子句对查询结果进行排序

## SQL数据更新功能

### 元组删除

```sql
DELETE  FROM  table_name
[ WHERE  search_condition ] ;
```

### 元组插入

```sql
INSERT
INTO  tabname [ ( colname { , colname … } ) ]
VALUES  ( expr | NULL { , expr | NULL … } ) | subquery;
```

可以插入常量元组（VALUES\(...\)），也可以将子查询结果插入\(subquery\)

### 元组修改

```sql
UPDATE  table_name
SET  colname = expr | NULL | subquery, ......
[ WHERE  search_condition ] ;
```

## 视图

视图的定义：

```sql
CREATE  VIEW  <视图名>  [ ( <列名> { , <列名> … } ) ]
AS  <映像语句>   [ WITH  CHECK  OPTION ]
```

视图:由若干张表经过映像语句构筑成的表，又称导出表

视图与基表的区别：被称为视图的二维表本身（结构与数据）并不实际存在于数据库内，而仅仅保留了其构造信息（有关视图的定义信息）。因此视图又被称为‘虚表’（virtual table）。当用户执行视图上的访问操作时，数据库管理系统将根据视图的定义命令将用户对于视图的访问操作转换成相应基表上的访问操作

### 视图的创建 \(CREATE VIEW\)

```sql
CREATE  VIEW  <视图名>  [ ( <列名> { , <列名> … } ) ]
AS  <映像语句>   [ WITH  CHECK  OPTION ]
```

如上创建一个以&lt;视图名&gt; 为表名的视图，对应的数据查询语句是&lt;映像语句&gt;。以&lt;映像语句&gt;作查询所得到的查询结果即是该视图中的元组。

如果没有给视图中的属性命名，则用&lt;映像语句&gt;的SELECT子句中的属性名作为视图属性的属性名。否则视图中的属性必需与&lt;映像语句&gt;的SELECT子句中的结果属性一一对应

WITH CHECK OPTION用于约束视图上的修改操作：如果允许在该视图上执行更新操作，则其更新后的结果元组仍然必需满足视图的定义条件。即通过该视图插入或修改后的新元组能够通过该视图上的查询操作查出来

**可以嵌套的创建视图**（即映像语句中FROM一个已经存在的视图）

### 视图的删除（DROP VIEW）

```sql
DROP VIEW <视图名>
```

**删除一个视图后，那些嵌套定义在该视图上的视图也会随之删除**

视图上可以进行查询操作\(实际上是先改写成基表上的查询操作\)

允许进行视图上的更新需要满足：视图的每一行必须对应基表的惟一一行，视图的每一列必须对应基表的惟一一列

视图的作用：提高了数据独立性，简化了用户观点，提供了自动的安全保护功能

