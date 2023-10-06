#  `db`常规操作

- 简介

  `MongoDB`是一个基于分布式文件存储 [1] 的数据库。由[C++](https://baike.baidu.com/item/C%2B%2B?fromModule=lemma_inlink)语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。

  `MongoDB`是一个介于[关系数据库](https://baike.baidu.com/item/关系数据库?fromModule=lemma_inlink)和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它支持的数据结构非常松散，是类似[`json`](https://baike.baidu.com/item/json?fromModule=lemma_inlink)的[`bson`](https://baike.baidu.com/item/bson?fromModule=lemma_inlink)格式，因此可以存储比较复杂的数据类型。`Mongo`最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立[索引](https://baike.baidu.com/item/索引?fromModule=lemma_inlink)。

- 特点
  1. 它的特点是高性能、易部署、易使用，存储数据非常方便。主要功能特性有：
  2. 面向集合存储，易存储对象类型的数据。
  3. 模式自由。
  4. 支持动态[查询](https://baike.baidu.com/item/查询?fromModule=lemma_inlink)。
  5. 支持完全索引，包含内部对象。
  6. 支持查询。
  7. 支持复制和故障恢复。
  8. 使用高效的二进制数据存储，包括大型对象（如视频等）。
  9. 自动处理碎片，以支持云计算层次的扩展性。
  10. 支持 [`Golang`](https://baike.baidu.com/item/Golang/2215139?fromModule=lemma_inlink)，[`RUBY`](https://baike.baidu.com/item/RUBY?fromModule=lemma_inlink)，[`PYTHON`](https://baike.baidu.com/item/PYTHON?fromModule=lemma_inlink)，[`JAVA`](https://baike.baidu.com/item/JAVA?fromModule=lemma_inlink)，[`C++`](https://baike.baidu.com/item/C%2B%2B?fromModule=lemma_inlink)，[`PHP`](https://baike.baidu.com/item/PHP?fromModule=lemma_inlink)，[C#](https://baike.baidu.com/item/C%23?fromModule=lemma_inlink)等多种语言。
  11. 文件存储格式为`BSON`（一种`JSON`的扩展）。
  12. 可通过[网络](https://baike.baidu.com/item/网络?fromModule=lemma_inlink)访问。

## `linux`环境下安装`mongodb`

下载`mongodb`并上传到`linux`   注意选择`linux`的`tgz`版本

解压`tar zxvf 你下载的压缩包`

进入你解压缩后的文件夹里创建`data`和`log`文件夹，并创建配置文件`mongodb.conf`

文件内容如下    注意格式为`yaml`格式

```shell
systemLog:
 destination: file
 path: ./log/mongod.log #日志文件夹
 logAppend: true #日志以追加的方式写入
storage:
 dbPath: ./data #数据文件夹  
 engine: wiredTiger 
 journal:
  enabled: true
net:
 bindIp: 0.0.0.0 # 此ip指支持远程连接  默认本地连接
 port: 27017  #端口
processManagement:
 fork: true  #后台启动
```

启动

方式一

`bin/mongod --port=27017 --dbpath=/data --logpath=/log/mongodb.log --bind_ip=0.0.0.0 --fork`

方式二

`bin/mongod -f conf/mongodb.conf`

## `mongo shell使用`

[详细操作查看官网](https://www.mongodb.com/docs/manual/reference/operator/)

`mongo shell`是`mongoDB`的交互式`js shell`界面 微操作人员提供直接测试数据库查询和操作的方法，在6.0以上的版本中压缩包中就不带有了

连接方式`mongo --host=127.0.0.1 --port=27017` 分别指定主机地址和端口

### 常用命令

| 命令                             | 说明                                 |
| -------------------------------- | ------------------------------------ |
| `show dbs |show databases`       | 显示数据库列表                       |
| use 数据库名                     | 切换数据库，如果不存在则创建该数据库 |
| `db.dropDatabase()`              | 删除数据库                           |
| `show collections | show tables` | 显示当前数据库的集合列表             |
| `db.集合名.stats()`              | 查看集合详情                         |
| `db.集合名.drop()`               | 删除集合                             |
| `show users`                     | 显示当前数据库的用户列表             |
| `show roles`                     | 显示当前数据库的角色列表             |
| `show profile`                   | 显示最近发生的操作                   |
| `load("xxx.js")`                 | 执行一个`js`脚本                     |
| `exit | quit()`                  | 退出当前shell                        |
| `help`                           | 查看`mangodb`支持哪些命令            |
| `db.help()`                      | 查询当前数据库支持的方法             |
| `db.集合名.help()`               | 显示集合的帮助信息                   |
| `db.version()`                   | 查看数据库版本信息                   |
| `interpreterVersion()`           | 查看JavaScript解释器版本             |

### 常用权限

| 权限名                 | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| `read`                 | 允许用户读取指定的数据库                                     |
| `readWrite`            | 允许用户读写指定的数据建库                                   |
| `dbAdmin`              | 允许用户在指定的数据库中执行管理函数，如索引的创建、删除、查看统计或访问`system.profile` |
| `userAdmin`            | 允许用户在指定的数据库中执行增删改查等                       |
| `clusterAdmin`         | 只在`admin`数据库中可用，赋予用户所有分片和复制集相关函数的管理权限 |
| `readAnyDatabase`      | 只在`admin`数据库中可用，赋予用户所有数据库的读权限          |
| `readWriteAnyDatabase` | 只在`admin`数据库中可用，赋予用户所有数据库的读写权限        |
| `userAdminAnyDatabase` | 只在`admin`数据库中可用，赋予用户所有数据库的`userAdmin`权限 |
| `dbAdminAnyDatabase`   | 只在`admin`数据库中可用，赋予用户所有数据库的`abAdmin`权限   |
| root                   | 只在`admin`数据库中可用，超级账号，超级权限                  |

- 用户认证 返回1表示成功

### 安全认证

shell连接上数据库后使用`admin`数据库，默认是不使用认证方式

创建一个管理员user

```go
use admin

db.createUser({user:"root",pwd:"950629",roles:["root"]})
```

创建数据库用户    

```go
use test
//为指定数据库创建用户，该用户只能使用该数据库   避免暴露管理员账号
db.createUser({user:"test",pwd:"950629",roles:["dbOwner"]})
```



之后关闭`mongodb`数据库

`bin/mongod conf/mongodb.conf --shutdown`

以认证方式启动

`bin/mongod conf/mongodb.conf --auth`

已认证方式启动之后的连接

- 管理员的连接方式

```go
mongo -uroot -p950629  //端口、主机地址为可选项 
```



- 指定数据库用户的连接方式

```go
mongo -u test -p 950629 --authencationDatabase=test //需指定数据库 端口、主机地址为可选项 
```





### 集合操作

```go
show tables  //或者 show collections

db.createCollection("emp") //创建一个emp集合

db.emp.drop() //删除集合
```

创建集合语法

```shell
db.createCollection(name,options)
```

options参数

| 字段   | 类型 | 描述                                                         |
| ------ | ---- | ------------------------------------------------------------ |
| capped | 布尔 | （可选）如果为true，则创建固定集合。固定集合是指有着固定大小的集合 |
| size   | 数值 | （可选）为固定集合指定一个最大数值（以字节记）。如果capped为true，也需指定该字段 |
| max    | 数值 | （可选）指定固定集合中包含文档的最大数量                     |

***注意：*** 如果集合不存在，先集合中插入文档也会创建集合

### 插入文档

3.2版本之后新增了`db.collection.insertOne()和db.collection.insertMany()`

#### 新增单个文档

- insertOne:支持writeConcern

```go
db.collection.insertOne(
	<document>,
    {
        writeConcern:<document>
    }
)
//writeConcern决定一个写操作落到多少个节点上才算成功。writeConcern的取值包括：
// 0:发起写操作，不关心是否成功；
// 1-n（集群最大节点数）：写操作落到多少个节点上才算成功
// majority：写操作需要复制到大多数节点上才算成功
```

- insert：若插入的数据主键已存在，则会抛 `DuolicateKeyException`异常，提示主键重复，不保存当前数据。
- save：如果_id逐渐存在则更新数据，如果不存在就插入数据

#### 编写`book.js`脚本插入数据

编写一个`js`脚本名字为`books.js`

加载脚本`load("books.js")`

### 查询文档

#### 条件查询

find查询集合中若干文档。语法如下

```go
db.collection.find(query,projection)
//query:可选，使用查询操作符指定查询条件
//projection：可选,使用投影操作符指定返回的键。查询时返回文档中所有的键值，只需省略该参数即可（默认省略）。投影时，id为1的时候，其他字段必须是1；id是0的时候，其他字段可以是0；如果没有_id字段的约束，多个其他字段必须是同为0或同为1
//如果查询返回的条目比较多，mingo shell会自动分批显示。默认情况下每次20条，使用it指令读取下一批
```

findOne查询集合中的第一个文档

```go
db.collection.findOne(query,projection)
```

条件查询示例

```go
db.collection.find({title:"books-20"}) //查询title为books-20的记录

db.collection.find({title:"books-20"},{title:1}) //查询title为books-20，且返回指定字段的记录
```

查询条件对照表

| sql  | mql            |
| ---- | -------------- |
| a=1  | `{a:1}`        |
| a<>1 | `{a:{$ne:1}}`  |
| a>1  | `{a:{$gt:1}}`  |
| a<1  | `{a:{$lt:1}}`  |
| a>=1 | `{a:{$gte:1}}` |
| a<=1 | `{a:{$lte:1}}` |

查询逻辑对照表

| sql          | mql                               |
| ------------ | --------------------------------- |
| a=1 AND b=1  | `{a:1,b:1}或{$and:[{a:1},{b:1}]}` |
| a=1 OR b=1   | `{$or:[]{a:1},{b:1}]}`            |
| a IS NULL    | `{a:{$exist:false}}`              |
| a IN (1,2,3) | `{a:{$in:[1,2,3]}}`               |

查询逻辑运算符

- `$lt:`存在并小于
- `$lte:`存在并小于等于
- `$gt:`存在并大于
- `$gte:`存在并大于等于
- `$ne:`不存在或存在不等于
- `$in:`存在并在指定的数组中
- `$nin:`不存在或不在指定的数组中
- `$or:`匹配多个条件中的一个
- `$and:`匹配全部条件

#### 分页&排序

##### 指定排序   

 sort()方法进行排序

```go
db.books.find({type:"travel"}).sort({price:-1})
```



##### 分页查询

skip用于跳过记录数，limit则用于限定返回结果数量。可以在执行find命令时同时指定skip、limit参数，以此实现分页的功能。

```go
db.books.find().skip(8).limit(4) //假定每页8条，查询第三页的文档
```

##### 正则表达式匹配查询

 使用`$regex`操作符来匹配字符串的正则表达式

```go
//使用正则表达式匹配type字段包含字符串so的记录
db.books.find({type:{$regex:"so"}})
//或
db.books.find({type:/so/})
```



### 更新文档

可以用update命令对指定的数据进行更新   格式如下

```go
db.collection.update(query,update,options)
//query:描述更新的查询条件
//update:描述更新的动作及新的内容
//options:描述更新的选项
//	upsert:可选，如果不存在update的记录，是否插入新的记录。默认false，不插入
//  multi:可选，是否按田间查询的多条记录全部更新。默认false，只更新找到的第一条记录
//  writeConcern:可选，决定一个写操作落到多少个节点上才算成功
```

更新操作符

| 操作符      | 格式                                        | 描述                                             |
| ----------- | ------------------------------------------- | ------------------------------------------------ |
| `$set`      | `{$set:{field:value}}`                      | 指定一个键并更新值，若键不存在则创建             |
| `$unset`    | `{$unset:{field:1}}`                        | 删除一个键                                       |
| `$inc`      | `{$inc:{field:value}`                       | 对数值类型进行增减                               |
| `$rename`   | `{$rename:{old_field_name:new_field_name}}` | 修改字段名称                                     |
| `$push`     | `{$push:{field:value}}`                     | 将数值增加到数组中，若数组不存在，则会进行初始化 |
| `$pushall`  | {$pushAll:{field:value_array}               | 追加多个值到一个数组字段内                       |
| `$pull`     | `{$pull:{field:value}}`                     | 从数组中删除指定元素                             |
| `$addToSet` | `{$addToSet:{field:value}}`                 | 添加元素到数组内，具有排重功能                   |
| `$pop`      | `{$pop:{field:1}}`                          | 删除数组的第一个或最后一个元素                   |

#### 更新单个文档

使用指令`db.collection.updateOne()`

或

```go
db.books.update({title:"nosql"},{$inc:{price:+3}})
```

#### 更新多个文档

使用`db.collection.updateMany()`命令

或

```go
db.books.update({title:"mysql"},{$set:{publishDate:new Date()}},{multi:true})
```

#### `upsert`指令

upsert是一种特殊的更新，如果发现匹配不到文档，则执行插入命令

```go
 db.books.update(
... {title:"my book"},
... {$set:{price:25,publishDate:new Date(),author:"sncot"}},
     ... {upsert:true})
```

#### 实现`replace`语义

`mmongodb`命令中的更新描述（update）通常由操作符描述，如果更新描述中不包含任何操作符，那么`MongoDB`会实现文档的replace语义

```go
db.books.update({title:"mysql"},{modifyTitle:"MySQL",price:89})
```

#### `findAndModify`命令

`findAndModify`命令兼并了查询和修改指定文档的功能，并只能修改单个文档

```go
db.books.findAndModify({
... query:{"_id" : ObjectId("632b93b738b2a977dbb7906e")},
... update:{$inc:{price:10}}
... })
```

类似的有

- `findOneAndUpdate:`更新单个文档并返回更新后的文档
- `findOneAndReplace:`替换单个文档斌返回替换后的文档

### 删除文档

 #### 使用remove删除文档

- remove命令需要配合查询条件使用
- 匹配查询条件的文档会被删除
- 指定一个空文档条件会删除所有文档
- remove命令会删除条件匹配的全部文档，如果明确希望删除一个，需指定justOne参数

语法如下

`db.collection.remove(query,justOne)`

示例

```go
db.books.remove({price:28}) //删除price为28的记录
db.books.remove({price:{$lt:100}}) //删除price小于100的记录
db.books.remove({}) //删除所有记录
db.books.remove() //报错

db.books.remove({type:"novel"},true) //删除满足type="novel"的首条记录
```





#### 使用delete删除文档

官方推荐使用`deleteOne()和deleteMany()`方法删除文档，语法如下

```go
db.books.deleteMany({})  //删除集合下全部文档
db.books.deleteMany({type:"novel"}) //删除type为novel的全部文档
db.books.deleteOne({type:"novel"}) //删除type为novel的一个文档
```

**注意：**remove和delete等命令是对文档逐个删除，如果希望全部删除使用 drop更高效

返回被删除文档

remove和delete等命令只会返回确认性信息，如果希望返回被删除的文档，则可以使用`findAndDelete`命令

```go
db.books.findAndDelete({type:"novel"})
```

除了在结果中返回删除文档，`findOneAndDelete`命令还允许定义'删除的顺序'，即按照指定顺序删除找到的第一个文档

```go
db.books.findOneAndDelete({type:"novel"},{sort:{price:1}})
```

remove、delete命令只能按默认顺序删除，利用这个特性，`findOneAndDelete`可以实现队列的先进先出

### 聚合操作

聚合操作处理数据记录并返回计算结果（如平均值、求和等），聚合操作组值来自多个文档，可以对分组数据进行各种操作返回单个结果。聚合操作有三类：单一作用聚合、聚合管道、`MapReduce`。

- 单一聚合：提供了堆场间距和过程的简单访问，操作都从单个聚合文档。
- 聚合管道：聚合管道是一个数据聚合的框架，模型基于数据处理流水线的概念。文档进入多级管道，将文档转换为聚合结果
- `MapReduce`：`MapReduce`操作具有两个阶段：处理每个文档冰箱每个输入文档发射一个或多个对象的map阶段，以及reduce组合map操作的输出阶段

#### 单一作用聚合

`mongodb`提供`db.collection.estimatedDocumentCount()、db.collection.count()、db.collection.distinct()`这类单一的聚合函数。所有这些操作都聚合来自单个集合的文档，虽然这些操作提供了对公共聚合的简单访问，但缺乏聚合管道和map-Reduce的灵活性和功能。

```go
db.books.count() //统计记录数量
db.books.find({price:109}).count() //统计price为109的文档个数
db.books.count({price:109}) //效果同上
db.books.estimatedDocumentCount({price:109}) //此方法等同与db.books.count(),此方法会忽略查询条件

db.books.distinct("price") //查询价格
```

**注意：**在分片集群上，如果存在鼓励文档或正在进行块迁移，则`db.collection.count()`没有查询谓词可能会导致计数不准确，避免这些情况，请在分片集群上使用db.collection.aggregate()方法

#### 聚合管道

`mongodb`聚合框架（Aggregation Framework）是一个计算框架，它可以

- 作用在一个或几个集合上
- 对集合中的数据进行一系列运算
- 将这些数据转化为期望的形式

从效果上说，聚合框架相当于sql查询中的group by、left outer join、as等

##### 管道（Pipeline）和阶段（Stage）

在整个聚合运算过程成为管道，他是有多个阶段组成，每个管道

- 接受一系列文档（原始数据）
- 每个阶段对这些文档进行一系列运算

管道操作语法

```go
pipeline = [$stage1,$stage2,......$stageN]
db.collection.aggregate(pipeline,{options})
//pipeline:一组数据聚合阶段。除$out、$Merge、$geonear外，每个阶段可以在管道中出现多次
//options：可选聚合操作的其它参数，包括：查询计划、是否使用临时文件、游标、最大操作时间、读写策略、强制索引等
```

常用管道聚合阶段

| 阶段             | 描述     | sql运算符       |
| ---------------- | -------- | --------------- |
| `$match`         | 筛选条件 | where           |
| `$project`       | 投影     | as              |
| `$lookup`        | 左外连接 | left outer join |
| `$sort`          | 排序     | order by        |
| `$group`         | 分组     | group by        |
| `$skip/$limit`   | 分页     |                 |
| `$unwind`        | 展开数组 |                 |
| `$graphLookup`   | 图搜索   |                 |
| `$facet/$bucket` | 分面搜索 |                 |

###### 起别名`$project`

```go
db.books.aggregate(
... {$project:{name:"$title",_id:0,price:1}}
... )
//name:"$title" 表示为title起别名name
//_id:0,price:1 表示返回结果中不显示_id字段，显示price字段
```

###### `$match`

用于对文档进行筛选，之后可以在得到的文档子集上做聚合，`$match`可以使用除了地理空间之外的所有常规查询操作符，在实际应用中尽可能将`$match`放在管道的前面位置。好处如下

- 快速将不需要的文档过滤掉，减少管道的工作量
- 如果在投影和分组之前执行`$match`，查询可以使用索引

```go
db.books.aggregate(
... {$match:{title:"nosql"}}
... )
```

###### `$count`

计数并返回与查询匹配的结果数

```go
db.books.aggregate([
... {$match:{title:"mysql"}},
... {$count:"price"}
... ])
```

###### `$group`

按指定的表达式对文档进行分组，并肩每个不同的分组输出到下一个阶段。输出的文档包含一个_id字段，该字段按键包含不同的组。

输出文档还可以包含计算字段，该字段保存由`$group`的_id字段分组的一些accumulator表达式的值。`$group

不会输出具体的文档而只是统计信息。

```go
{$group:{_id:<expression>,<field1>:{<accumulator>:<expression>},...}}
//id字段是比太难的，但是可是指定id字段为null来为整个输入文档计算累计值
//剩余的计算字段是可选的，并使用<accumulator>运算符进行运算
//_id和<accumulator>表达式可以接受任何有效的表达式
```

###### `accumulator`操作符

| 名称          | 描述                                                         | 类比sql   |
| ------------- | ------------------------------------------------------------ | --------- |
| `$avg`        | 计算均值                                                     | avg       |
| `$first`      | 返回每组第一个文档，如果有排序则按照排序，没有则按照存储顺序的第一个文档 | limit 0,1 |
| `$last`       | 返回每组的最后一个文档，有排序则按照排序，没有则按照存储顺序 |           |
| `$max`        | 根据分组，获取集合中所有文档对应值的最大值。                 | max       |
| `$min`        | 根据分组，获取集合中所有文档对应值的最小值。                 | min       |
| `$push`       | 将指定的表达式的值添加到一个数组中                           |           |
| `$addToSet`   | 将表达式的值添加到一个集合中（无重复值、无序）               |           |
| `$sum`        | 计算总和                                                     | sum       |
| `$stdDevPop`  | 返回输入值的总体标准差（population standard deviation）      |           |
| `$stdDevSamp` | 返回输入值的样本标准差（the sample standard deviation）      |           |

`$group`阶段的内存限制为100M.默认情况下，如果stage超过此限制，`$group`将产生错误。但是要允许产生大型数据集，请将allowDiskUse选项设置为true以启用`$group`操作写入临时文件

==book的数量，收藏总数和平均值==

```go
db.books.aggregate([
... {$group:{_id:null,count:{$sum:1},pop:{$sum:"$favCount"},avg:{$avg:"$favCount"}}}
... ])
```

==先对作者分组，在对书名分组，再求和==

```go
db.books.aggregate([
... {$group:{_id:{name:"$author.name",title:"$title"},pop:{$sum:"$favCount"}}}
... ])
```

==每个作者type合集==

```go
db.books.aggregate([
... {$group:{_id:"$author.name",types:{$addToSet:"$type"}}}
... ])
```

###### `$unwind`

可以将数组拆分为单独的文档

v3.2+支持以下语法

```GO
{
    $unwind:{
        path:<field path>,//#要指定字段路径，在字段名前加$并用双引号括起来
        includeArrayIndex:<string> ,//可选，一个新字段的名称用于存放数组索引，该名称不能以$开始
        preserveNullAndEmptyArrays:<boolean>//可选，default:false,若为true，缺少或为空数组，则$unwind输出文档
    }
}
```

==姓名为xx006的作者的book的tag数组拆分为多个文档==

```go
db.book.aggregate([
    {$match:{"author.name":"xx006"}},
    {$unwind:"$tag"}
])
```

==每个作者的tag合计==

先以tag拆分，在合并

```go
db.books.aggregate([
    {$unwind:"$tag"},
    {$group:{_id:"$author.name",types:{$addToSet:"$tag"}}}
])
```

```go
//使用includeArrayIndex选项来输出数组元素的数组索引并且把没有tag标签的文档输出
db.books.aggregate([
    {$match:{"author.name":"fox"}},
    {$unwind:{path:"$tags",includeArrayIndex:"arrayIndex",preserveNullAndEmptyArrays:true}}
])
```

###### `$limit`

限制传递到管道下一阶段的文档数量

```go
db.books.aggregate([
    {$limit:3}
])
```

此操作把前三个文档传递给管道的下一操作

**注意：**当`sort`在limit之前出现时，`sort`只维持前n个文档，n是指定的限制，而mongodb只将n个结果存储在内存中

###### `$skip`

跳过进入stage指定数量的文档，并肩其余文档传递到下一个阶段

```go
db.books.aggregate([
    {$skip:5}    //跳过5个文档
])
```

此操作跳过上一操作传递给它的前五个文档，skip对管道传递的文档内容没有影响

###### `$sort`

对文档进行排序，并将排序结果传递给下一个操作，可以指定升序或降序   

可以对多个字段进行排序

```go
db.books.aggregate([
    {$sort:{facCount:-1,title:1}}  //先对favCount降序排序，遇到相同的对title进行升序排序
])
```



###### `$lookup`

v3.2+支持，主要用来多表关联查询，相当于关系型数据库中的多表关联查询，每个输入的文档，经过$lookup阶段的处理，输出的文档会包含一个新生成的数组（可根据需要命名新key）。数组列存放的是来自被join集合的适配文档，如果没有，集合为空（即[ ]）。

```go
db.collection.aggregate([
    {$lookup:{
        from:"<collection to join>",
        localField:"<field from the input documents>",
        foreignField:"<field from the documents of the from collection>",
        as:"<output array field>"
    	}
    }
])
```

| 属性         | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| from         |                                                              |
| localField   | 源集合中的match值，如果输入的集合中，某文档没有localField这个字段，在待处理的过程中会默认此文档含有localField：null的键值对 |
| foreignField | 待join的集合的match值，如果待join的集合中，文档没有foreignField值，在处理的过程中，会默认此文档有foreignField：null的键值对 |
| as           | 为输出文档的新增值命名。如果输入的集合中已存在，则会覆盖掉   |

**注意：**null==null为真

```go
 db.order.aggregate([
	{$lookup:{
 		from:"customer",
 		localField:"customerCode",
 		foreignField:"customerCode",
 		as:"customerOrder"
 		}
    },
	{$lookup:{
		from:"orderItem",
		localField:"orderId",
		foreignField:"orderId",
		as:"orderI"
 		}
    }
	])
/*结果如下
{
        "_id" : ObjectId("632e53da51a432e82cf65710"),
        "orderId" : 3,
        "orderCode" : "order003",
        "customerCode" : "custom03",
        "price" : 400,
        "customerOrder" : [
                {
                        "_id" : ObjectId("632e56fa51a432e82cf65713"),
                        "customerCode" : "custom03",
                        "phone" : "1221342",
                        "address" : "中国香港"
                }
        ],
        "OrderItem" : [
                {
                        "_id" : ObjectId("632e59c551a432e82cf65716"),
                        "itemId" : 3,
                        "productName" : "peers",
                        "quatity" : 4,
                        "orderId" : 3
                }
        ]
}
*/
```

###### `$bucket`

区间

```go
db.books.aggregate([
    {$bucket:{
        groupby:"$favCount",
        boundaries:[0,10,60,80,100],
        default:"other",
        output:"{$count:{$sum:1}}"
    }}
])
```

#### 导入数据

##### `1、`下载工具

[mongodb/database-tools](https://www.mongodb.com/try/download/mongosync)

###### `2、`解压压缩包

里面有四个文件分别是bin、LICENSE、README、THIRD-PARTY-NOTICES.

bin文件夹里有八个可执行文件，本次导入仅使用mongoimport([使用介绍](https://www.mongodb.com/docs/database-tools/mongoimport/))，其他可执行文件的使用介绍可去官网查看

3、下载邮政编码数据集作为导入的数据[下载地址](https://media.mongodb.org/zips.json)

使用工具把数据导入

```go
mongoimport -h 43.143.76.231 -d dbTest -u dbTest -p 950629 --authenticationDatabase=dbTest -c zips --file=C:\Users\Desktop\zips.json
//参数说明
/*
-h 代表连接的主机地址
--port 代表远程连接的数据库的端口
-u/--username 数据库认证用户名
-p/--password 数据库认证用户的密码
-d/--db 代表要链接的数据库
-c/--collection 代表要把数据插入的collection
-f/--field 代表导入集合的字段 
--type 代表导入的文件类型 包括csv和json、tsv，默认json格式
--file 导入的文件路径及名称
--headerline 导入csv文件时指明第一行是列明，不需要导入
*/
```

==返回人口超过1000万的州==

```go
db.zips.aggregate([
    {$group:{_id:"$state",totalPop:{$sum:"$pop"}}},
    {$match:{totalPop:{$gt:10*1000*1000}}}
])
```

