# MySQL语法

## 创建数据库

`create database 数据库名`

## 删除数据库

`drop database 数据库名`

## 选择数据库

`use 数据库名`

## 创建数据表

`create table 表名(列名 列类型)`

```sql
	CREATE TABLE IF NOT EXISTS `表名`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `title` VARCHAR(100) NOT NULL,
   `author` VARCHAR(40) NOT NULL,
   `date` DATE,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```
### 实例解析：

* 如果你不想字段为 NULL 可以设置字段的属性为 NOT NULL， 在操作数据库时如果输入该字段的数据为NULL ，就会报错。
* AUTO_INCREMENT 定义列为自增的属性，一般用于主键，数值会自动加1。
* PRIMARY KEY 关键字用于定义列为主键。 您可以使用多列来定义主键，列间以逗号分隔。
* ENGINE 设置存储引擎，CHARSET 设置编码。

## 删除表

`drop table 表名`

## 插入数据

`insert into 表名(列名1，列名2，列名3...) values (值1，值2，值3)

*如果不写列名代表插入全部属性都需要写。*

## 查询

`select 列名1 as 别名1, 列名2， 列名3 from 表名1，表名2 【where 条件 】【limit n】【offset m】  `

* 查询语句中你可以使用一个或者多个表，表之间使用逗号(,)分割，并使用WHERE语句来设定查询条件。
* SELECT 命令可以读取一条或者多条记录。
* 你可以使用星号（*）来代替其他字段，SELECT语句会返回表的所有字段数据
* 你可以使用 WHERE 语句来包含任何条件。
* 你可以使用 LIMIT 属性来设定返回的记录数。
* 你可以通过OFFSET指定SELECT语句开始查询的数据偏移量。默认情况下偏移量为0。

## where子句

`select field1, field2,...fieldN from table_name1, table_name2...【WHERE condition1 【AND 【OR】】 condition2.....`

* 查询语句中你可以使用一个或者多个表，表之间使用逗号, 分割，并使用WHERE语句来设定查询条件。
* 你可以在 WHERE 子句中指定任何条件。
* 你可以使用 AND 或者 OR 指定一个或多个条件。
* WHERE 子句也可以运用于 SQL 的 DELETE 或者 UPDATE 命令。
* WHERE 子句类似于程序语言中的 if 条件，根据 MySQL 表中的字段值来读取指定的数据。

| 操作符 | 描述                                                         | 实例                 |
| :----- | :----------------------------------------------------------- | :------------------- |
| =      | 等号，检测两个值是否相等，如果相等返回true                   | (A = B) 返回false。  |
| <>, != | 不等于，检测两个值是否相等，如果不相等返回true               | (A != B) 返回 true。 |
| >      | 大于号，检测左边的值是否大于右边的值, 如果左边的值大于右边的值返回true | (A > B) 返回false。  |
| <      | 小于号，检测左边的值是否小于右边的值, 如果左边的值小于右边的值返回true | (A < B) 返回 true。  |
| >=     | 大于等于号，检测左边的值是否大于或等于右边的值, 如果左边的值大于或等于右边的值返回true | (A >= B) 返回false。 |
| <=     | 小于等于号，检测左边的值是否小于于或等于右边的值, 如果左边的值小于或等于右边的值返回true | (A <= B) 返回 true。 |

>上表取自菜鸟教程
>
## uodate 更新

`update 表名 set 列名1=新值1, ...,列名N=新值N 【where condition】`

* 可以在 WHERE 子句中指定任何条件。
* 可以同时更新一个或多个字段。
* 可以在一个单独表中同时更新数据。

当我们需要将字段中的特定字符串批量修改为其他字符串时，可已使用以下操作：

`UPDATE table_name SET field=REPLACE(field, 'old-string', 'new-string')[WHERE Clause]`

## delete

删除表中数据

`delete from 表名 where ...`

* 速度: drop>truncate>delete
* truncate也能删表数据.
* 只有delete能回滚

## like

`SELECT field1, field2,...fieldN FROM table_name WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'`

* 可以在 WHERE 子句中指定任何条件。
* 可以在 WHERE 子句中使用LIKE子句。
* 可以使用LIKE子句代替等号 =。
* LIKE 通常与 % 一同使用，类似于一个元字符的搜索。
* 可以使用 AND 或者 OR 指定一个或多个条件。
* 可以在 DELETE 或 UPDATE 命令中使用 WHERE...LIKE 子句来指定条件。

```
'%a'     //以a结尾的数据
'a%'     //以a开头的数据
'%a%'    //含有a的数据
'_a_'    //三位且中间字母是a的
'_a'     //两位且结尾字母是a的
'a_'     //两位且开头字母是a的
```

```
在 where like 的条件查询中，SQL 提供了四种匹配方式。

1. %：表示任意 0 个或多个字符。可匹配任意类型和长度的字符，有些情况下若是中文，请使用两个百分号（%%）表示。
2. _：表示任意单个字符。匹配单个任意字符，它常用来限制表达式的字符长度语句。
3. []：表示括号内所列字符中的一个（类似正则表达式）。指定一个字符、字符串或范围，要求所匹配对象为它们中的任一个。
4. [^] ：表示不在括号所列之内的单个字符。其取值和 [] 相同，但它要求所匹配对象为指定字符以外的任一个字符。
5. 查询内容包含通配符时,由于通配符的缘故，导致我们查询特殊字符 “%”、“_”、“[” 的语句无法正常实现，而把特殊字符用 “[ ]” 括起便可正常查询。

```

## union

>用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据。
>

### 语法

```
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions];
```

### 参数

* expression1, expression2, ... expression_n: 要检索的列。

* tables: 要检索的数据表。

* WHERE conditions: 可选， 检索条件。

* DISTINCT: 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。

* ALL: 可选，返回所有结果集，包含重复数据。

## 排序
```
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
ORDER BY field1 [ASC [DESC][默认 ASC]], [field2...] [ASC [DESC][默认 ASC]]
```

* 你可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。
* 你可以设定多个字段来排序。
* 你可以使用 ASC 或 DESC 关键字来设置查询结果是按升序或降序排列。 默认情况下，它是按升序排列。
* 你可以添加 WHERE...LIKE 子句来设置条件。

## 分组

```
SELECT column_name, function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```

## 连接的使用

```
SELECT a.id, a.author, b.count FROM tbl a [INNER JOIN] tcount_tbl b ON a.runoob_author = b.runoob_author;
```
* ON 后边是条件
* INNER JOIN（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
* LEFT JOIN（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
* RIGHT JOIN（右连接）： 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。

## 对于空值处理

* IS NULL: 当列的值是 NULL,此运算符返回 true。
* IS NOT NULL: 当列的值不为 NULL, 运算符返回 true。
* <=>: 比较操作符（不同于=运算符），当比较的的两个值为 NULL 时返回 true。
* 关于 NULL 的条件比较运算是比较特殊的。你不能使用 = NULL 或 != NULL 在列中查找 NULL 值 在 MySQL 中，NULL 值与任何其它值的比较（即使是 NULL）永远返回 false，即 NULL = NULL 返回false 。
* MySQL 中处理 NULL 使用 IS NULL 和 IS NOT NULL 运算符。

## alert

1. `mysql> ALTER TABLE testalter_tbl  DROP i `
2. `mysql> ALTER TABLE testalter_tbl ADD i INT `
3. `mysql> ALTER TABLE testalter_tbl MODIFY c CHAR(10) `
4. `mysql> ALTER TABLE testalter_tbl CHANGE i j BIGINT `
`mysql> ALTER TABLE testalter_tbl CHANGE j j INT `
5. 当你修改字段时，你可以指定是否包含值或者是否设置默认值。以下实例，指定字段 j 为 NOT NULL 且默认值为100 。`mysql> ALTER TABLE testalter_tbl MODIFY j BIGINT NOT NULL DEFAULT 100` 如果你不设置默认值，MySQL会自动设置该字段默认为 NULL。

### alter其他用途：

修改存储引擎：修改为myisam

```
alter table tableName engine=myisam;
```

删除外键约束：keyName是外键别名

```
alter table tableName drop foreign key keyName;
```

修改字段的相对位置：这里name1为想要修改的字段，type1为该字段原来类型，first和after二选一，这应该显而易见，first放在第一位，after放在name2字段后面

```
alter table tableName modify name1 type1 first|after name2;
```

## 防止表重复

### 保证数据的唯一性:

1. 在 MySQL 数据表中设置指定的字段为 PRIMARY KEY（主键） 或者 UNIQUE（唯一） 索引.

2. INSERT IGNORE INTO 与 INSERT INTO 的区别就是 INSERT IGNORE 会忽略数据库中已经存在的数据，如果数据库没有数据，就插入新的数据，如果有数据的话就跳过这条数据。这样就可以保留数据库中已经存在数据，达到在间隙中插入数据的目的。


3. 另一种设置数据的唯一性方法是添加一个 UNIQUE 索引，如下所示：

```
CREATE TABLE person_tbl
(
   first_name CHAR(20) NOT NULL,
   last_name CHAR(20) NOT NULL,
   sex CHAR(10),
   UNIQUE (last_name, first_name)
);
```

4. 统计重复数据
以下我们将统计表中 first_name 和 last_name的重复记录数：

```
mysql> SELECT COUNT(*) as repetitions, last_name, first_name
    -> FROM person_tbl
    -> GROUP BY last_name, first_name
    -> HAVING repetitions > 1;
    
```

### 以上查询语句将返回 person_tbl 表中重复的记录数。 一般情况下，查询重复的值，请执行以下操作：

* 确定哪一列包含的值可能会重复。
* 在列选择列表使用COUNT(*)列出的那些列。
* 在GROUP BY子句中列出的列。
* HAVING子句设置重复数大于1。
* 过滤重复数据
* 如果你需要读取不重复的数据可以在 SELECT 语句中使用 DISTINCT 关键字来过滤重复数据。

```
mysql> SELECT DISTINCT last_name, first_name FROM person_tbl;
```

* 你也可以使用 GROUP BY 来读取数据表中不重复的数据：

```
mysql> SELECT last_name, first_name FROM person_tbl GROUP BY (last_name, first_name);
```

* 删除重复数据
如果你想删除数据表中的重复数据，你可以使用以下的SQL语句：

```
mysql> CREATE TABLE tmp SELECT last_name, first_name, sex FROM person_tbl  GROUP BY (last_name, first_name, sex);
mysql> DROP TABLE person_tbl;
mysql> ALTER TABLE tmp RENAME TO person_tbl;
```

* 当然你也可以在数据表中添加 INDEX（索引） 和 PRIMAY KEY（主键）这种简单的方法来删除表中的重复记录。方法如下：

```
mysql> ALTER IGNORE TABLE person_tbl ADD PRIMARY KEY (last_name, first_name);
```

**以上笔记主要是在看"菜鸟教程"与网上查资料后整理,详细教程自行百度搜索**