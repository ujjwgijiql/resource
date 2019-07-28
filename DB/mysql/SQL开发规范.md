# 一、前言
SQL(Standard Query Language)是数据库开发的工业标准，是随着业务而诞生的结构化查询语言其中包含6个部分：

1 数据查询语言（DQL:Data Query Language）：

其语句，也称为“数据检索语句”，用以从表中获得数据，确定数据怎样在应用程序给出。保留字SELECT是DQL（也是所有SQL）用得最多的动词，其他DQL常用的保留字有WHERE，ORDER BY，GROUP BY和HAVING。这些DQL保留字常与其他类型的SQL语句一起使用。

2 数据操作语言（DML：Data Manipulation Language）：

其语句包括动词INSERT，UPDATE和DELETE。它们分别用于添加，修改和删除表中的行。也称为动作查询语言。

3 事务处理语言（TPL）：

它的语句能确保被DML语句影响的表的所有行及时得以更新。TPL语句包括BEGIN TRANSACTION，COMMIT和ROLLBACK。

4 数据控制语言（DCL）：

它的语句通过GRANT或REVOKE获得许可，确定单个用户和用户组对数据库对象的访问。某些RDBMS可用GRANT或REVOKE控制对表单个列的访问。

5 数据定义语言（DDL）：

其语句包括动词CREATE和DROP。在数据库中创建新表或删除表（CREAT TABLE 或 DROP TABLE）；为表加入索引等。DDL包括许多与人数据库目录中获得数据有关的保留字。它也是动作查询的一部分。

6指针控制语言（CCL）：

它的语句，像DECLARE CURSOR，FETCH INTO和UPDATE WHERE CURRENT用于对一个或多个表单独行的操作。

# 二、索引
索引的设计类似与目录其大大减少了查询的检索行数分为B-tree索引，hash索引，位图索引等

我们通常用到的索引一般指B-tree索引(技术上称为B+tree索引其结构为平衡二叉树变种)

2.1 B-tree索引匹配
索引匹配分为全值匹配、最左前缀匹配、值范围匹配、列前缀匹配等。

CREATE TABLE MAN(ID INT NOT NULL,

NAME VARCHAR(20) NOT NULL ,

CARD VARCHAR(18) NOT NULL ,

address VARCHAR(100) NOT NULL ,

.

.

KEY(ID,NAME,CARD));

全值匹配: select id,name,card from man;

最左前缀匹配： select id from man;

值范围匹配：select name from man where id >=?

列前缀匹配：select name from man where  id like '2%'

2.2 创建最优索引
索引不能随意创建，索引本身也是一张表需要维护成本，对于小表全表扫描往往性能高于索引扫

描，对于数量庞大的表我们需采取分表形式来做性能优化， 索引最适合中大级别表.

2.2.1 索引创建与删除
   在MySQL中创建索引语法为：

create index <index_name> on <table_name>(<column_name1>,<column_name2>..);

或 alter table <table_name> add index <index_name(<column_name1>,<column_name2>..);

   删除索引：alter table <table_name> drop index <index_name>;

   理论上在索引包含字段超过4个则其效率将大大减小，索引尽可能将索引列控制在3列以内

2.2.2 索引创建选择
如2.1中 若该表数据 address 字段分布极不均 有以下SQL

select name ,address from man where name =? and address=?

            此时可选索引创建有 index(NAME),INDEX2(ADDRESS) ，

INDEX(NAME,ADDRESS) 、INDEX(ADDRESS,NAME)

最佳索引为index(name,address)

我们常见的错误就是为每个列创建单独索引其性能并非最佳,往往不能提高性能

我们应选择key分布均匀的字段作为索引最左前缀创建联合索引，其可识别率最高

表设计时应当避免字段null值

2.3 索引长度计算
1.所有的索引字段，如果没有设置not null，则需要加一个字节。

2.定长字段，int占四个字节、date占三个字节、char(n)占n个字符。

3.对于变成字段varchar(n)，则有n个字符+两个字节。

4.不同的字符集，一个字符占用的字节数不同。latin1编码的，一个字符占用一个字节，gbk编码的，一个字

符占用两个字节，utf8编码的，一个字符占用三个字节。

# 三、编写高性能SQL
我们在开发的过程中往往会遇到SQL运行缓慢，甚至某条慢sql拖垮整个服务的情况，因此编写高性能的sql

语言对于项目开发格外重要。

1、查询语句应明确查询字段，避免臃肿SQL。

2、明确业务逻辑尽可能用简单语句完成需求，尽量做到少关联、少子查询。

3、where条件左边应避免使用函数、表达式.如:select id from orders where date_add

(current_date ,interval 3 day))=？避免使用不明确条件如<>、>等 具体原理可参考索引数据结构原理。

4、尽可能做到group by 、order by中的表达式只涉及到一张表的列 以保证查询可使用索引。

5，设计查询语句时，分析是否需要将复杂查询差分为多个查询

6、养成少执行多看执行计划的习惯，少执行以免低性能sql造成的数据库高资源利用，sql编写原则上避免全

   表扫描、覆盖索引扫描， 执行计划表现为type字段为 full 、index(mysql)，关联查询确保关联字段有索引

7、如有使用到union 尽可能加上all，mysql在执行union 时会自动在临时表加上distinct 关键字会导致

   mysql对整个临时表做唯一性检测。