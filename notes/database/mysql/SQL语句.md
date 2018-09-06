## SQL语句
SQL语句分为两个部分：数据操作语言(DML)和数据定义语言(DDL)。
增删改查指令构成了SQL的DML部分：
* SELECT 从数据库获取数据
* UPDATE 更新数据库表
* DELETE 删除数据
* INSERT INTO 向数据库中插入数据

DDL语句包括对数据库、表、索引等的操作:
* CREATE DATABASE 创建数据库
* ALTER DATABASE 修改数据库
* CREATE TABLE 创建数据库表
* ALTER TABLE 修改表(结构)
* DROP TABLE 删除表
* CREATE INDEX 创建索引
* DROP INDEX 删除索引

### JOIN用法
join用于根据两个或多个表中的列之间的关系，从这些表中查询数据。

#### INNER JOIN
内连接，可以省略inner关键字，获取两个表中字段匹配关系的记录，只有join两边都存在的
行才会获取到。INNER JOIN即获取两个集合的交集

#### LEFT JOIN
left join、right join和full join都属于外链接(outer join)
其中left join是指包含左边集合的全部数据(即便右边集合没有对应的数据)，
以及交集部分右侧的数据
#### RIGHT JOIN 
right join是指包含右边集合的全部数据(即便左边集合没有对应的数据)，
以及交集部分右侧的数据
#### FULL JOIN
full join会返回左右两个表中的所有数据，但是MySQL原生不支持full join，
可以通过union配合left join和right join实现

### UNION和UNION ALL
* UNION 操作符用于合并两个或多个 SELECT 语句的结果集。
* UNION 内部的 SELECT 语句必须拥有相同数量的列。
列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。
* UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL。
```sql
SELECT column_name(s) FROM table_name1
UNION
SELECT column_name(s) FROM table_name2
```
### SQL约束
1. NOT NULL
约束强制列不接受 NULL 值

2. UNIQUE 约束
UNIQUE 约束唯一标识数据库表中的每条记录

3. PRIMARY KEY 约束
PRIMARY KEY 约束唯一标识数据库表中的每条记录。
主键必须包含唯一的值。
主键列不能包含 NULL 值。
每个表都应该有一个主键，并且每个表只能有一个主键。

4. FOREIGN KEY 约束
一个表中的 FOREIGN KEY 指向另一个表中的 PRIMARY KEY。

5. CHECK 约束
CHECK 约束用于限制列中的值的范围。
如果对单个列定义 CHECK 约束，那么该列只允许特定的值。
如果对一个表定义 CHECK 约束，那么此约束会在特定的列中对值进行限制。

6. DEFAULT 约束
DEFAULT 约束用于向列中插入默认值。
如果没有规定其他的值，那么会将默认值添加到所有的新记录。

### SQL函数
需要注意的是在使用合计函数如sum,avg，不能使用where子句需要使用having代替。

语句	语法
```sql
1. AND / OR	
SELECT column_name(s)
FROM table_name
WHERE condition
AND|OR condition

2. ALTER TABLE (add column)	
ALTER TABLE table_name 
ADD column_name datatype

3. ALTER TABLE (drop column)	
ALTER TABLE table_name 
DROP COLUMN column_name
4. AS (alias for column)	
SELECT column_name AS column_alias
FROM table_name

5. AS (alias for table)	
SELECT column_name
FROM table_name  AS table_alias

6. BETWEEN	
SELECT column_name(s)
FROM table_name
WHERE column_name
BETWEEN value1 AND value2

7. CREATE DATABASE	
CREATE DATABASE database_name

8. CREATE INDEX	
CREATE INDEX index_name
ON table_name (column_name)

9. CREATE TABLE	
CREATE TABLE table_name
(
column_name1 data_type,
column_name2 data_type,
.......
)

10. CREATE UNIQUE INDEX	
CREATE UNIQUE INDEX index_name
ON table_name (column_name)

11. CREATE VIEW	
CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition

12. DELETE FROM	
DELETE FROM table_name 
(Note: Deletes the entire table!!)
%or
DELETE FROM table_name
WHERE condition

13. DROP DATABASE	
DROP DATABASE database_name

14. DROP INDEX	
DROP INDEX table_name.index_name

15. DROP TABLE	
DROP TABLE table_name

16. GROUP BY	
SELECT column_name1,SUM(column_name2)
FROM table_name
GROUP BY column_name1

17. HAVING	
SELECT column_name1,SUM(column_name2)
FROM table_name
GROUP BY column_name1
HAVING SUM(column_name2) condition value

18. IN	
SELECT column_name(s)
FROM table_name
WHERE column_name
IN (value1,value2,..)

19. INSERT INTO	
INSERT INTO table_name
VALUES (value1, value2,....)
%or
INSERT INTO table_name
(column_name1, column_name2,...)
VALUES (value1, value2,....)

20.LIKE	
SELECT column_name(s)
FROM table_name
WHERE column_name
LIKE pattern

21. ORDER BY	
SELECT column_name(s)
FROM table_name
ORDER BY column_name [ASC|DESC]

22. SELECT	
SELECT column_name(s)
FROM table_name

23. SELECT *	
SELECT *
FROM table_name

24. SELECT DISTINCT	
SELECT DISTINCT column_name(s)
FROM table_name

25. SELECT INTO
(used to create backup copies of tables)	
SELECT *
INTO new_table_name
FROM original_table_name
%or
SELECT column_name(s)
INTO new_table_name
FROM original_table_name

26. TRUNCATE TABLE
(deletes only the data inside the table)	
TRUNCATE TABLE table_name

27. UPDATE	
UPDATE table_name
SET column_name=new_value
[, column_name=new_value]
WHERE column_name=some_value

28. WHERE	
SELECT column_name(s)
FROM table_name
WHERE condition
```

### 常规SQL语句优化
1. 不建议使用`select *`查找所有列名

2. 选择最适用的字段属性，选择的数据结构越小越好

3. 使用连接(join)来代替子查询

4. 使用union代替手动创建临时表

5. 使用事务
多使用commit及时提交数据库事务

6. 尽量减少表的查询次数
