### `MySQL`

***注意：*

	- 根据规范语句应严格区分大小写，但因为`mysql`的不严谨，`mysql`对大小写不敏感
	- 根据规范字符串应用单引号包围，`msyql`的不严谨使`mysql`对单引号和双引号不敏感

#### `mysql语言的分类`

##### `DDL`

(data definition languages 数据定义语言)这些不同的数据库、表、视图、索引等数据库对象，还可以用来创建、删除、、修改数据库和数据表的结构。

- 主要的语句关键字包括 create、drop、alter、rename、truncate等



##### `DML`

（data manipulation language 数据操作语言）用于添加、删除、更新和查询数据库记录，并检查数据完整性。

- 主要的关键字包括 insert、delete、update、select等
- select是`sql`语句的基础，最为重要

##### `DCL`

（data control language 数据控制语言）用于定义数据库、表、字段、用户的访问权限和安全级别

- 主要的关键字包括grant、remove、commit、rollback、savepoint、revoke等

**注意：**

- 因为使用的频繁，所以很多人把查询语句单独作为一类：DQL(数据查询语言)。
- 还有单独将commit、rollback取出来称为TCL(transaction control language 事务控制语言)

### `mysql`语句

#### 创建数据库

格式：`CREATE DATABASE 数据库名;`

#### 使用数据库

格式：`USE 数据库名;`

#### 创建表

格式：`CREATE TABLE 表名(字段名 字段类型,字段名 字段类型...);`

#### 查询

格式

`SELECT 字段名,... FROM 表; `

#### 起别名

格式

`SELECT 字段名 别名，字段名 AS 别名,字段名 "别名" FROM 表;`

AS 是alias（别名）的缩写， 以上是起别名的三种形式，双引号用于别名中有空格时。

#### 去重

关键字：DISTINCT

格式

`SELECT DISTINCT 字段明,字段名,... FROM 表名;`

#### 着重

反引号``

在使用一些不可避免的情况下如存在一个表名为order的表，在查询时可用着重号。

#### 显示表结构

关键字：`DESCRIBE` 或`DESC`

示例 `DESC user;`

#### 查询常量

格式  `SELECT '尚硅谷',name,age FROM user;`

尚硅谷为非表中字段，当执行以上语句时每条记录前都会加上常量尚硅谷



#### 空值参与运算

用于当某天记录中的某个字段为空，却要参与运算时。

示例：

`SELECT name,salary*(1+rate)*12 "年终奖" FROM user;`

当rate为NULL时结果必为空

解决方法如下

`SELECT name,salary*(1+IFNULL(rate,0))*12 "年终奖" FROM user;`

#### 过滤筛选

格式：`SELECT 字段名,... FROM 表名 WHERE 过滤条件;`

**注意：** where应紧跟在from后面