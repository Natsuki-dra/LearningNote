# MongoDB怎么玩

## 创建数据库

```sql
use basename
```

当basename不存在时，则自动创建一个数据库，但此时数据库由于没有数据，因此不会被持久化。切换数据库也同样是用这条指令。

在给该数据库插入数据后，此数据库才会在show dbs时显示存在

## 显示数据库列表

```sql
show dbs
```

该指令用于显示所有的数据库

mongodb默认的数据库是test，如果没有创建新的数据库，则数据会自动保存在test中。

## 显示当前数据库

```shell
db
```

## 删除数据库

```sql
db.dropDatabase()
```



## 插入数据（创建集合）

语法格式如下面两条

db已经代指当前数据库(固定语法）,collection需替换为目标集合名

```shell
db.collection.insertMany();
db.collection.insertOne();
```

### insertMany()

添加一个json字符串组，用中括号[]来将json字符串括起来

### insertOne（）

一次添加一个json字符串

## 创建集合

当需要创建一个有上限的集合，或者创建一个对文档进行校验的集合时，用下面的这个方法

```shell
db.createCollection(name,options);
```

该方法对数据库命令create进行了包装

在4.2版本中的新特性：移除了MMAPv1存储引擎及其设置

```shell
db.createCollection( <name>,
   {
     capped: <boolean>,	
     autoIndexId: <boolean>,	
     size: <number>,
     max: <number>,
     storageEngine: <document>,
     validator: <document>,
     validationLevel: <string>,
     validationAction: <string>,
     indexOptionDefaults: <document>,
     viewOn: <string>,              // Added in MongoDB 3.4
     pipeline: <pipeline>,          // Added in MongoDB 3.4
     collation: <document>,         // Added in MongoDB 3.4
     writeConcern: <document>
   }
)
```

| 域               | 类型     | 描述                                                         |
| ---------------- | -------- | ------------------------------------------------------------ |
| capped           | boolean  | 默认为false，如果要创建一个有限集，需要设置为true，并且同时要设置size的大小 |
| autoIndexId      | boolean  | 默认开启，如果集合中不需要”_id"这一属性，请设置为false。注：从4.0版本开始，本地数据库以外的数据库不能够将该选项关闭 |
| size             | number   | 为集合设置文档大小设置上限值（以字节为单位）。当集合文档数达到了最大值，数据库将会自动将最早的记录移除。 |
| max              | number   | 为集合设置文档数目的上限，size域中的值将会优先于max执行。如果文档的字节数比目数更早达到上限，会把最早的文档删除 |
| storageEngine    | document | 集合的存储引擎。当需要指定存储引擎时，用如下的格式来指定：{<storage-engine-name>:<options>}在复制期间，验证创建集合时集合时指定的存储引擎配置并将其记录到oplog中，以支持具有使用不同存储引擎的成员的副本集 |
| validator        | document | 允许用户指定集合的验证规则或者表达式。**注：1.验证步骤发生在更新和插入操作的时候。已经存在的文档不会被验证机制检查到，知道对其进行更新。2.不能为admin，local，config数据库添加验证规则。3.不能给system.*集合创建集合** |
| validationLevel  | string   | 验证严格等级：1.“off”：不会对插入和更新操作验证。2.“strict”：默认开启，对所有插入操作和更新操作进行检查。3.“moderate”：允许验证规则对存在的有效的文档进行检查，不允许在更新无效的文档时进行检查 |
| validationAction | string   | 决定验证发生错误时是否被使操作失败。“error”：默认选项。在写操作进行前必须通过验证检查。否则不予进行写操作。“warn”：文档不需要通过验证。如果文档验证时报错，写操作会报告验证失败。 |



## 查找数据（find）

```shell
db.collection.find({})
```

上式为最简单的查找语句，查找目标集合中所有文档。

一份文档打印一行（行空间足够情况下）

若需要每一个属性一行，可使用下面的：

```shell
db.collection.find({}).pretty()
```

运用pretty()来格式化文档。

```shell
db.collection.find({<field>:<value>,...})
```

### 筛选文档

有条件地提取文档,返回field域为value值地文档。

其中，域值可以有多种方式填充：

1.域名：字符串

```shell
//寻找status域为“D”的文档
db.collection.find({status:"D"});
```

2.域名：数字

```shell
//寻找qty域值为0的文档
db.collection.find({qty:0});
```

3.多个域指对

```shell
//多个条件
db.collection.find({qty:0,status:"D"});
```

4.嵌套字段

```shell
//某一阈值存放的是一个对象如out:{field1：value1，field2:value2}
//可以解析嵌套的字符串来选择文档
db.collection.find({"out.field2":value2});
```

5.域名：对象

```shell
db.collection.find({out:{field1:value1,field2:value2}});
```

6.域名：数组单个元素

```shell
//现有一个域存放了一个数组
//color:["red","black","blue"]
//查找color域中包含“red”的文档
db.collection.find({color:"red"});
//如果color域是一个字符串，那么其就会使用等式匹配来筛选文档
```

7.域名：数组

```shell
//或者直接根据数组精确查找文档，此时，不仅要求数组的元素一致，且每个元素的顺序必须一样
db.collection.find({color:["red","black","red"]});
```

### 修整结果

​		查询文档时不是所有域指的信息都需要知道，此时我们要向服务端指明我们需要的域。

​		同前面条件筛选一样，视图文档的表单也是用｛｝括起，并在find（）函数中。

​		注：如果要查看所有的文档，需要前面加一对占位的大括号。

```shell
//查看所有文档的items域和status域，以及默认显示的"_id"域
db.collection.find({},{item:1,status:1});
//想要排除"_id"域的话，就将"_id"设置为0；
db.collection.find({},{_id:0,item:1,status:1});
```

<field>:1表示将该域加入视图文档中。

<field>:0表示将该域移除视图文档中。

## 创建索引

​		在mongodb中，默认的索引是**_id**域。索引支持mongodb能够高效的查找数据。没有索引，mongodb将会遍历整个集合。

​		索引用一些特殊的数据结构存储集合里面一小部分数据，并且会以一种简单的方式遍历。索引存放着某一指定的域的值或集合。并根据其值进行排序。经过排序的索引支持高效的等式匹配以及范围查询操作。不仅如此，mongoDB还会根据索引的顺序来对返回结果进行排序。

​		基本上，mongodb里的索引跟其他数据库系统的索引比较像。mongoDB将索引定义在集合层级并支持索引为任何域或者次级域。

### 默认_id索引

​		mongodb在创建collection是会自动创建一个\_id唯一索引。该索引会阻止客户端插入两条一相同\_id值的索引。而且该索引也不会被删除。

### 自定义索引

```shell
db.collection.createIndex(<key and index type specification>,<option>)

//例子
//在name域上创建一个降序的索引
db.collection.createIndex({name:-1})
```

该方法只能在不存在相同规范的索引时创建索引

**MongoDB索引使用的是B树数据结构**

索引的名字

一般来说，索引的

# MongoDB概念

1.mongodb将文档存在于collection（集合）中，Collection好比关系型数据库中的表。如果集合不存在时，mongodb会在你第一次将数据存进集合的时候创建集合。

2.每一个数据单元是以**文档document**形式存储在集合中，类似于关系型数据库中的行。不过文档中的域的数量是任意的，不像关系型数据库中限制性那么强。

3.什么是bson？

根据mongodb官方文档的解释：bson是json的一种二进制表示，但是比其json它能够存储表示更多样的数据格式。（比如Date和BinData类型）。其优点是灵活性高，但是空间利用率不是很理想。

主要有三个特点：轻量性、可遍历性、高效性。

BSON是由10gen开发的一个数据格式，目前主要用于mongodb。

与json的区别：

1.更快的遍历速度（可以跳跃读取数据）：json要跳过一个文档进行数据读取，需要对文档进行扫描才行，要进行麻烦的数据结构匹配（如括号），而json将每一个元素的长度存在元素的头部，这样就只需要读取元素的长度来进行读取。

2.操作更简单：数据存储是无类型的。在json从9到10，后面所有元素都需要向后移动一位。而在bson中，可以指定这个列为数字，因此不会需要将后面元素移动。

3.增加了额外的数据类型：增加了byte array数据类型。使得二进制存储不再需要先base64转换后再存成json。大大减少了计算开销和数据大小

总的来说，bson在json的基础上添加了一个目录。而且对于有些数据会预留空间造成空间利用率没有json高。

4.文档的域名限制：

​		**_id**域名用于主键。这个域值必须在集合中为唯一的值，而且不能改变，可以是数组以外的任何类型。

​		域名不能包含空字符。

​		顶级域名不能够以**$**开头。而在MongoDB3.6开始，服务器允许域名包含点.和符号$。

​		**注：当询问语句中包含一些字符串时，mongodb的询问语句分析不能每一次都完美的解释询问命令的意思。**

​		BSON允许拥有重复的域名，但是mongodb不推荐这样做。





