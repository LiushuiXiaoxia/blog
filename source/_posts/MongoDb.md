---
title: 浅谈MongoDB数据库
date: 2017-09-28 18:39:17
categories: 数据库
tags:
    - MongoDB
    - Java MongoDB
---

# 浅谈MongoDB数据库

---
<!-- TOC -->

## 简介

最近项目中需要分析Http报文，并且需要用数据库保存，刚刚开始打算用Mysql，后来咨询了老司机，老司机建议使用MongoDB来实现，所以特写一篇文章来总结下。

<!-- more -->

## MongoDB 介绍

> MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

上面是百度百科的介绍，这里是MongoDB的[官网](https://www.mongodb.com/)。

简单来说MongoDB是一种数据库，不过是非关系型数据库，它的一些概念和数据库不太一样。

MongoDB中一些概念和普通数据库不太一样，普通数据库有database、table、row、field的概念，MongoDB依次叫database、collection、document、field，这个在后面的代码示例会有体现。

### 数据库安装

因为用的是Mac，简单介绍下MongoDB在Mac上面的安装，Windows用户可以参考官网。

Mac上安装，直接使用Homebrew就可以了，如果不清楚Homebrew是什么，可以参考[这里](https://brew.sh/)。

brew 安装MongoDB

```bash
brew install mongodb
```

结果如下

```bash
$ brew install mongodb
Updating Homebrew...
==> Downloading https://homebrew.bintray.com/bottles/mongodb-3.4.9.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring mongodb-3.4.9.sierra.bottle.tar.gz
==> Caveats
To have launchd start mongodb now and restart at login:
  brew services start mongodb
Or, if you don't want/need a background service you can just run:
  mongod --config /usr/local/etc/mongod.conf
==> Summary
🍺  /usr/local/Cellar/mongodb/3.4.9: 19 files, 284.9MB
```

上面给的信息说明很清楚，如果当做一个服务器来用，直接用`brew services start mongodb`就行了，如果只是作为一个程序，那么用`mongod --config /usr/local/etc/mongod.conf`启动，`/usr/local/etc/mongod.conf`是默认配置文件。

我们可以看下配置文件内容，分别是日志路径、存储路径。最后一个是IP访问设置，默认只能本地访问，如果其他机器需要访问这个数据库，需要在配置文件中添加对应的IP。假如想省事，可以设置为`0.0.0.0`，那么任意机器都是可以访问的。

```bash
$ cat /usr/local/etc/mongod.conf
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb
net:
  bindIp: 127.0.0.1
```

## MongoDB使用

我这里就简单开启一个MongoDB服务，直接调用`brew services start mongodb`就行了，这样MongoDB就启动了。

使用`mongo`命令即可进入MongoDB控制台。

![](http://upload-images.jianshu.io/upload_images/1520343-acd14283311d877c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```bash
show dbs                     show database names
show collections             show collections in current database
show users                   show users in current database
show profile                 show most recent system.profile entries with time >= 1ms
show logs                    show the accessible logger names
show log [name]              prints out the last segment of log in memory, 'global' is default
use <db_name>                set current database
db.foo.find()                list objects in collection foo
```

下面简单演示数据库的增删改查，具体用法请参考[官方CRUD文档](https://docs.mongodb.com/manual/crud/)。

创建一个`abc123`的database

```bash
use abc123
```

创建一个`user`的collection，并插入两条数据。

```bash
> db.user.insert({name:'aaa'})
WriteResult({ "nInserted" : 1 })

> db.user.insert({name:'bbb',age:22})
WriteResult({ "nInserted" : 1 })
```

查看`user`中的数据

```bash
> db.user.find()
{ "_id" : ObjectId("59bf7bde5d6768f6ee06de2b"), "name" : "aaa" }
{ "_id" : ObjectId("59bf7d045d6768f6ee06de2c"), "name" : "bbb", "age" : 22 }
```

根据条件查询

```bash
> db.user.find({name:'bbb'})
{ "_id" : ObjectId("59bf7d045d6768f6ee06de2c"), "name" : "bbb", "age" : 22 }
```

修改数据

```bash
> db.user.updateOne({name:'aaa'},{$set:{age:11}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.user.find();
{ "_id" : ObjectId("59bf7bde5d6768f6ee06de2b"), "name" : "aaa", "age" : 11 }
{ "_id" : ObjectId("59bf7d045d6768f6ee06de2c"), "name" : "bbb", "age" : 22 }
```

删除数据

```bash
> db.user.deleteMany({name:'aaa'})
{ "acknowledged" : true, "deletedCount" : 1 }

> db.user.find()
{ "_id" : ObjectId("59bf7d045d6768f6ee06de2c"), "name" : "bbb", "age" : 22 }
```

刚刚开始说过，Mongo的数据结构是类似于json的数据结构，数据里面的"_id"就是主键，是系统自己生成的。

当然也可以自己指定，在插入数据的时候，指定"_id"字段即可。

```bash
> db.user.insert({"_id":"12345",name:"Hello"})
WriteResult({ "nInserted" : 1 })

> db.user.find()
{ "_id" : ObjectId("59bf7d045d6768f6ee06de2c"), "name" : "bbb", "age" : 22 }
{ "_id" : "12345", "name" : "Hello" }

> db.user.insert({"_id":"12345",name:"Hello"})
WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "E11000 duplicate key error collection: abc123.user index: _id_ dup key: { : \"12345\" }"
	}
})
```

上面示例表示，第一次插入数据的时候，是成功的，查询结果也是符合预期，当再次执行的时候，插入失败，说明主键冲突。


### MongoDB可视化工具

上面使用的是命令行工具查看数据，可能对有些同学不是很优化，我在这里推荐一款可视化功工具[Robo 3T](http://blog.robomongo.org/)

用Robo 3T查看数据就很方便了。

![](http://upload-images.jianshu.io/upload_images/1520343-2988a2e07e9d3743.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Java操作MongoDB

Java操作Mongo比较简单，直接调用MongoDB的驱动即可，其他部分基本上和上面的语法一样。

本次使用的版本是`3.5.0`，下面是依赖方式。

```xml
<dependencies>
    <dependency>
        <groupId>org.mongodb</groupId>
        <artifactId>mongodb-driver</artifactId>
        <version>3.5.0</version>
    </dependency>
</dependencies>
```

```groovy
  dependencies {
    compile 'org.mongodb:mongodb-driver:3.5.0'
  }
```

数据库连接

```java
    private MongoClient client;

    @Before
    public void before() {
        String url = "mongodb://127.0.0.1:27017/abc123";
        MongoClientURI uri = new MongoClientURI(url);
        client = new MongoClient(uri);
    }
```

查询数据

```java
    @Test
    public void testFind() {
        MongoDatabase database = client.getDatabase("abc123");
        MongoCollection<Document> collection = database.getCollection("user");

        for (Document document : collection.find()) {
            for (Map.Entry<String, Object> entry : document.entrySet()) {
                System.out.print(entry.getKey() + ":" + entry.getValue() + "\t");
            }
            System.out.println();
        }
    }
```

根据先前的数据，查到的结果2条。

```
_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello
```

添加数据

```java
    @Test
    public void testInsert() {
        testFind();

        MongoDatabase database = client.getDatabase("abc123");
        MongoCollection<Document> collection = database.getCollection("user");
        Document document = new Document();
        document.put("name", "World");
        document.put("age", 33);
        collection.insertOne(document);

        testFind();
    }
```

插入前总共为2条数据，插入后为3条数据。

```
_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello

_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello
_id:59bf8282fe37ad04a81fd012	name:World	age:33
```

修改数据

```java
    @Test
    public void testUpdate() {
        System.out.println("before update");
        testFind();

        MongoDatabase database = client.getDatabase("abc123");
        MongoCollection<Document> collection = database.getCollection("user");
        collection.updateMany(
                Filters.eq("name", "World"),
                Updates.set("age", 44)
        );

        System.out.println();
        System.out.println();
        System.out.println("after update");
        testFind();
    }
```

修改数据和其他的不一样，`collection.updateMany`有2个参数，第一个参数`Filters.eq("name", "World")`是查询条件，第二个参数`Updates.set("age", 44)`为赋值语句。

在修改前数据为`_id:59bf8282fe37ad04a81fd012	name:World	age:33`，修改后就变成了`_id:59bf8282fe37ad04a81fd012	name:World	age:44`。

```
before update
_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello
_id:59bf8282fe37ad04a81fd012	name:World	age:33


after update
_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello
_id:59bf8282fe37ad04a81fd012	name:World	age:44
```

删除数据

```java

    @Test
    public void testDelete() {
        System.out.println("before delete");
        testFind();

        MongoDatabase database = client.getDatabase("abc123");
        MongoCollection<Document> collection = database.getCollection("user");
        collection.deleteMany(
                Filters.eq("name", "World")
        );

        System.out.println();
        System.out.println();
        System.out.println("after delete");
        testFind();
    }
```

删除前有三条数据，删除后只有2条数据。

```
before delete
_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello
_id:59bf8282fe37ad04a81fd012	name:World	age:44


after delete
_id:59bf7d045d6768f6ee06de2c	name:bbb	age:22.0
_id:12345	name:Hello
```

## 总结

总的来说，MongoDB使用还是很简单的，相对于传统的关系型数据库，优缺点表现如下。

**优点:**

* 面向文档存储(类JSON数据模式简单而强大)

* 动态查询

* 全索引支持,扩展到内部对象和内嵌数组

* 查询记录分析

* 快速,就地更新

* 高效存储二进制大对象 (比如照片和视频)

* 复制和故障切换支持

* Auto- Sharding自动分片支持云级扩展性

* MapReduce 支持复杂聚合

* 商业支持,培训和咨询

**缺点:**

* 不支持事务（进行开发时需要注意，哪些功能需要使用数据库提供的事务支持）

* MongoDB占用空间过大 （不过这个确定对于目前快速下跌的硬盘价格来说，也不算什么缺点了）

* MongoDB没有如MySQL那样成熟的维护工具，这对于开发和IT运营都是个值得注意的地方

* 在32位系统上，不支持大于2.5G的数据(很多操作系统都已经抛弃了32位版本，所以这个也算不上什么缺点了，3.4版本已经放弃支持32 位 x86平台)

## 相关资料

[MongoDB的官网](https://www.mongodb.com/)

[Homebrew](https://brew.sh/)

[官方CRUD文档](https://docs.mongodb.com/manual/crud/)

[Robo 3T](http://blog.robomongo.org/)

[MongoDB 的优点和缺点](http://blog.csdn.net/zdc524/article/details/46967413)