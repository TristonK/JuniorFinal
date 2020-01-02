# 数据库安全性与完整性

> 4.1 数据库的安全性保护
>
> * 数据库安全的基本概念与内容：主体，客体，身份标识与鉴别，自主访问控制，审计
> * SQL对数据库安全的支持
>   * SQL中的存取权限
>   * SQL中的授权命令GRANT和权限的回收命令REVOKE
>
>     4.2    数据库的完整性保护
> * 数据库完整性保护的功能：目的与常用实现措施
> * 实体完整性，参照完整性，用户定义完整性
> * 完整的CREATE TABLE命令
> * 基表的创建
> * 完整性约束的定义：主关键字，外关键字，CHECK约束，UNIQUE，NOT NULL, DEFAULT
> * 触发器及其创建命令

数据库的保护：在数据库的管理系统内部设置一些必要的软件以达到数据保护的目的

## 数据库的安全性保护

数据库的安全要求用户：通过规定的访问途径，按照规定的访问规则

可信计算基TCB：为实现数据库安全所采用的所有实施策略与机制的集合

**主体**：数据库中数据的访问者

**客体**：数据库中的数据及其载体

**身份标识与鉴别**：每个主题有一个标识符与一个用于验证身份的访问口令，主体访问客体时，TCB将对主体所提交的身份标识符与口令进行鉴别，以防止非法访问

**自主访问控制\(DAC\)**：一种基于存取矩阵的安全控制模型，基于用户的鉴别与存取访问规则的确定。每个用户要给以对系统中每个存取对象的存取权限，一个主体访问客体时，系统检查该主体在该客体上是否拥有所需要的权限。

对于存取权限，只有两种获取方式，一种是客体所有者自动拥有全部权限，另一种是拥有权限的用户自主的将他的权限传授给其他用户

DAC将在登录和访问数据库时检查权限

强制访问控制：主体无权将任何权限授予他人

**审计**：跟踪用户对数据的访问操作，如访问时间/访问内容/用户名/终端名/操作类型/操作结果，并可以根据审计结果给出报警信息

这个安全策略需要一个网络中的实体完成，即访问监控器，在TCB中实现

### 安全标准

用户自主保护级：自主访问控制、身份鉴别、数据完整性

系统审计保护级：自主访问控制、身份鉴别、客体重用、审计、数据完整性

安全标记保护级：自主访问控制、强制访问控制、标记、身份鉴别、客体重用、审计、数据完整性

结构化保护级：自主访问控制、强制访问控制、标记、身份鉴别、客体重用、审计、数据完整性、隐蔽信道分析、可信路径

访问验证保护级：自主访问控制、强制访问控制、标记、身份鉴别、客体重用、审计、数据完整性、隐蔽信道分析、可信路径、可信恢复

### SQL对安全的支持

自主访问控制通过\(用户，操作对象，操作权限\)这样的三元组来定义用户对数据的访问权限

授权范围：SELECT权、INSERT权、DELETE权、UPDATE权、REFERENCE权、EXECUTE权、USAGE权

授权：

```sql
Grant  <权限>  on  表名[(列名)]  to  用户 With  grant  option

GRANT <权限> ON <数据对象> FROM <数据库用户>
```

回收语句：

```sql
REVOKE  <操作权限列表>  ON  <操作对象>
FROM  <用户名列表>  [RESTRICT | CASCADE]
```

CASCADE：连锁回收

RESTRICT：在不存在连锁回收问题时才能回收权限，否则拒绝回收

## 数据库的完整性保护

完整性包括了正确性\(数据有效有意义\)与一致性（多用户访问时保证对数据的更新与访问保持一致）

数据库完整性保护：

* 执行更新操作时，检查是否违反完整性约束条件，防止又存取权的合法用户的误操作
* 目的：
  * 及时发现错误
  * 采取措施防之错误进一步蔓延
  * 最终将数据库恢复正确状态 
* 常用实现措施
  * 完整性约束条件的定义与检查
  * 触发器
  * 并发控制技术

保护基本功能：设置功能、检查功能、处理功能

完整性规则的三个内容：

* 实体完整性：基表主关键字不为空
* 参照完整性：关系R中的每个元组在外键F上的值要么为被引用的关系中存在的值，要么为空值
* 用户定义完整性：用户定义的数据完整性要求

一条完整性约束规则由三个部分组成：

* 完整性约束条件的设置：
  * 属性级的约束（域约束）：数据类型、非空值约束、取值范围约束
  * 元组级别的约束（表约束）：主键、候选键定义\(UNIQUE\)、外键定义、基于元组的CHECK子句
  * 全局约束（断言assertion）：单个关系中设计统计操作的约束条件、多个关系之间复杂的约束条件
* 条件的检查（DBMS内部设置）
* 条件的处理：如破坏完整性，可能拒绝执行并报警，也可能调用相应的函数处理如外键定义子句中的方法或者触发器中给出的方法

约束的命名：`CONSTRAINT <约束名> <完整性约束定义子句>`

完整的CREATE TABLE命令需要定义：

* 模式名&表名
* 属性的定义
  * 属性名&数据类型
  * 缺省值定义 DEFAULT{default\_constant \| NULL}
  * 属性的数据约束定义\(见下\)
* 表级（元组级）的数据约束定义\(见下\)

属性的约束定义：

```sql
{    NOT NULL   |
     [ CONSTRAINT constraint_name ]
          UNIQUE
        | PRIMARY KEY
        | CHECK ( search_condition )
        | REFERENCES table_name [ ( column_name ) ]
            [ ON DELETE CASCADE | RESTRICT | SET NULL ]
            [ ON UPDATE CASCADE | RESTRICT | SET NULL ] }
```

多个属性取值约束定义（表级约束定义），需要另起一行

```sql
[ CONSTRAINT constraint_name ]
   {   UNIQUE ( colname { , colname ... } )
    | PRIMARY KEY ( colname { , colname ... } )
    | CHECK ( search_condition )
    | FOREIGN KEY ( colname { , colname ... } )
        REFERENCES table_name [ ( colname { ,colname... } ) ]
            [ ON DELETE CASCADE | RESTRICT | SET NULL ]
            [ ON UPDATE CASCADE|RESTRICT|SET NULL ]   }
```

UNIQUE可以取空值

候选键：UNIQUE + NOT NULL

对于外键REFERENCES KEY后面会加上取值约束与保证一致性的保证措施

Forengen Key约束：

```sql
FOREIGN KEY ( colname { , colname ... } )
  REFERENCES table_name [ ( colname { ,colname... } ) ]
    [ ON DELETE CASCADE | RESTRICT | SET NULL ]
    [ ON UPDATE CASCADE | RESTRICT | SET NULL ]   }
```

Foreign key ...... References ......:定义主外键的引用关系。当对引用表中的外键进行赋值时，需要检查外键值的正确性。

on delete ...... / on update ......: 当在被引用表中删除元组或修改主键值时，需要维护引用表中外键值的正确性，具体方式如下：

* Cascade：同步做连带删除/更新
* Restrict：如果在引用表中存在与被删除或修改的主键相关的元组，则拒绝本次对被引用表的delete/update操作 \(缺省的维护模式\)
* Set Null：如果在引用表中存在与被删除或修改的主键相关的元组，则自动地将相关元组上的外键值置为空\(NULL\)

CHECK:其他任意的属性取值约束

定义断言 `CREATE ASSERTION <name> CHECK( <condition> )`

撤消断言 `DROP ASSERTION <assertion-name-list>`

触发器:某个事件的发生导致另外一些事情的发生，以消除前一个事件对数据完整性的影响

触发器的组成：

* 触发事件\(用户定义\)：为某个完整性约束条件的否定或者某种数据操纵事件
* 结果事件\(用户定义\)：触发发生后，消除影响的程序，通常是一组SQL命令
* 触发过程：DBMS自动调用并执行

创建命令：

```sql
CREATE TRIGGER trigger_name { BEFORE | AFTER }
{ INSERT | DELETE
  | UPDATE [ OF colname { , colname ... } ] }
    ON table_name 
[ REFERENCING corr_name_def { , ...... } ] /*给新/之前的命名*/
/*执行事件*/
[ FOR EACH ROW | FOR EACH STATEMENT ]
[ WHEN ( search_condition ) ]
{ statement
| BEGIN ATOMIC statement; { statement; ... } END
```

命名方式：

```sql
{       OLD [ ROW ] [ AS ] old_row_corr_name
| NEW [ ROW ] [ AS ] new_row_corr_name

| OLD TABLE [ AS ] old_table_corr_name
| NEW TABLE [ AS ] new_table_corr_name    }
```

删除触发器： `DROP TRIGGER trigger_name`

举例：

```sql
/*删除一个客户元组时，需要将该客户所有订单上的cid置为空值(set  null)*/
create  trigger  foreign_cid  
after  delete  on  customers
referencing  old  as  old_custom
for  each  row
begin
update  orders
set  cid = null
where  cid = :old_custom.cid ;
end;
```

