什么是数据

data数据，在计算机中数据的含义是广泛的，例如视频、音频等都是数据

关系型数据库（Relational DBMS）

表结构

### win卸载：

- 停止服务
- 控制面板卸载、删除相关文件夹
- 清理注册表的中HML-SYSTEM-C、搜索mysql
- C:ProgramDate\mysqlC:ProgramDate\mysql

### win安装：

·····

默认三个表：

information_schema:内置数据库，粗存系统账户信息。**不能删除**

mysql：内置数据库，存储mysql配置信息，不能删除

test：内置数据库，测试用，可删除

### SQL语句介绍

结构化查询语言简称SQL-不区分大小写

### SQL语句分类

数据定义语言：DDl 用来定义数据库对象<font color=red size =5px>creat,alert,drop</font>

数据操作语言：DML对数据库表中的记录进行操作<font color=red size =5px>insert,delete,update</font>

数据控制语言：DCL用来定义数据库的访问权限和安全级别，及创建用户

数据查询语言：DQL用来查询数据库中表的记录<font color=red size =5px>select，from,where</font>

### SQL语句例子

use DATABASE_NAME;使用xx数据库

select database();查看当前使用的数据库

修改数据库的编码格式和排序规则：alter database db_name

mysql常见的数据类型

int double varcher date

varcher可变长度的字符

show tables;查询当前数据库下面的表

desc table_name显示表结构

show variables like 'character%';查询MySQL的编码格式

临时改变dos窗口的编码格式

set character_set_client=gbk;

set character_set_results=gbk;

delete from table_name where

truncate table重建归零 

select **<font color = red>distinct</font>** name from student查询name，去重

between x and x包含

x in(x,x,x)在举列中

like ''模糊查询 %匹配任意多个字符 _匹配一个字符

排序查询

order by x desc,x desc多个排序，在查询最后ordey by 

asc升序，dese降序

LIMIT：限制结果集、分页查询

（页码-1）*页面大小，页面大小

now()可以加秒数

不等于<> !=

空或非空查询	IS NULL IS　NOT NULL

NOT IN()不包含NOT LIKE  NOT NOT BETWEEN AND 取反

分组查询

```sql
select MAX(SAL),JOB from EMP group by JOB
```

HAVING与GROUP BY 连用 ,对group by分组后的结果再次筛选

- SELECT
- FROM
- WHERE
- ​	LIKE
- GROUP BY
- HAVING
- ORDER BY
- DESC/ASC
- LIMIT

子查询

```sql
select * from emp where depno=(select depno from emp where ename='allen')
```

子查询的列只能是=前的

并集，交集，差集

查询的列要一致

主外键关联

```
ALTER TABLE EMPLOYEE ADD FOREIGN KEY[外键名字](KEY) REFERENCES DEPARTMENT(KEY)
```

```
show create table 表名
```

查看创建表的SQL，可以看到外键名

```mysql
alert table 表名 drop foregin key 外键名
```

表连接（JOIN）

```sql
--内连接
select xx from A inner join B on A.x=B.x
```

```mysql
--左外连接
select xx from A LEFT JOIN B on a.x=b.x
--以左表为基准表,右表没数据用null
```

```mysql
--右外连接
select xx from A RIGHT JOIN B on a.x=b.x
--以右 表为基准表,左表没数据用null
```

```mysql
--多表联查
select xx 
from x inner join x
on x
inner join x
on x
where x
```

### 索引

可以提高查询性能

单列索引，一个索引只包含一个列

组合索引，一个索引包含多个列

#### 索引类型

```
show index from 表名 ，查看表中的索引
```

主键索引

唯一索引：

```mysql
alter table 表名 add UNIQUE(列)
```

普通索引：

```
alter table table_name add index 索引名(列名)
```

组合索引：

```sql
alter table table_name add index 索引名(列1,列2)
```

全文索引：

```
alter table table_name add fulltext(列名)
```

innoDB不支持全文索引，myisam支持

删除索引：

```
drop index index_name on 表名
```

索引设计原则

where子句条件出现的列，连接子句的列

少量的数据无须加索引

使用短索引

```
create index indexNmae on mytable
```

不要过度索引

#### 数据库优化

架构：

​	主从复制，读写分离，负载均衡（mycat）分库分表

​	innodb增删改快，支持事务

​	myisam查询快，支持全文索引

从SQL方面优化



```sql
--修改字段为主键
alter table testoa add constraint testoa_pk primary key(id) 
--修改字段为自增
alter table testoa change id id int auto_increment

如果该字段不是主键，需要先设置该字段为主键：
alter table 表名 add primary key(字段名);
修改字段为自动增长
alter table 表名 change 字段名 字段名 字段类型 auto_increment;
```

