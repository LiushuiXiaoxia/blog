---
title: MongoDB查询总结
date: 2017-10-24 18:22:03
categories: 数据库
tags:
    - MongoDB
    - Map reduce
---

# MongoDB查询总结

---


## 介绍

前面写过一篇关于Mongodb的例子——[浅谈MongoDB数据库](http://www.jianshu.com/p/9e1d34eb1d4c)，当时使用的只是简单的查询，然后后面业务变的有点复杂，原先没有仔细研究过Mongodb的查询，以为就是简单调用下`find`就可以了，乃衣服。

所以今天特地举例说明一下Mongo中查询问题。

Mongo查询可以分为2种：

- 普通查询，类似于Sql中的 `select where`

- 聚合查询，类似于Sql中的 `group by`

<!-- more -->

## 普通查询

首先放一下[官方文档](https://docs.mongodb.com/manual/tutorial/query-documents/)，普通查询主要用到`db.collection.find()`函数。

定义下示例数据库，下面是是初始化数据，可以在Mongo中的控制台执行。

```js
db.inventory.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
```

- 查询所有

```js
db.inventory.find( {} )
```

映射Sql语句

```sql
SELECT * FROM inventory
```

- 条件查询

语法格式

```js
{ <field1>: <value1>, ... }
```

比如查询`status`为`D`记录。

```js
db.inventory.find( { status: "D" } )
```

映射Sql语句

```sql
SELECT * FROM inventory WHERE status = "D"
```

- 使用操作符进行条件查询

语法格式

```js
{ <field1>: { <operator1>: <value1> }, ... }
```

比如查询满足`status`是数组`[A,D]`中的记录

```js
db.inventory.find( { status: { $in: [ "A", "D" ] } } )
```

映射Sql语句

```sql
SELECT * FROM inventory WHERE status in ("A", "D")
```

- AND 条件查询

直接在find函数指定多个字段满足即可，这样就是 and 条件。

比如下面语句就是 `status` 为 `A`，`qty` 小于 `30`。

```js
db.inventory.find( { status: "A", qty: { $lt: 30 } } )
```

映射Sql语句

```sql
SELECT * FROM inventory WHERE status = "A" AND qty < 30
```

- OR 条件查询

OR 和 AND 就不一样了，需要用到操作符 `$or`，如下所示。

```js
db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
```

类似于SQL中的

```sql
SELECT * FROM inventory WHERE status = "A" OR qty < 30
```

- OR 和 AND 集合一起

```js
db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
```

表示这样的意思。

```sql
SELECT * FROM inventory WHERE status = "A" AND ( qty < 30 OR item LIKE "p%")
```

### 查询举例

- 查询全部

```sql
SELECT *
FROM people
```

```js
db.people.find()
```

- 指定字段

```sql
SELECT id,
       user_id,
       status
FROM people
```

```js
db.people.find(
    { },
    { user_id: 1, status: 1 }
)
```

```sql
SELECT user_id, status
FROM people
```

- 指定字段，不显示`_id`

```js
db.people.find(
    { },
    { user_id: 1, status: 1, _id: 0 }
)
```

- 条件查询全部

```sql
SELECT *
FROM people
WHERE status = "A"
```

```js
db.people.find(
    { status: "A" }
)
```

- 条件查询指定字段

```sql
SELECT user_id, status
FROM people
WHERE status = "A"
```

```js
db.people.find(
    { status: "A" },
    { user_id: 1, status: 1, _id: 0 }
)
```

- 条件查询不等于

```sql
SELECT *
FROM people
WHERE status != "A"
```

```js
db.people.find(
    { status: { $ne: "A" } }
)
```

- 条件查询 AND

```sql
SELECT *
FROM people
WHERE status = "A"
AND age = 50
```

```js
db.people.find(
    { status: "A",
      age: 50 }
)
```

- 条件查询 OR

```sql
SELECT *
FROM people
WHERE status = "A"
OR age = 50
```

```js
db.people.find(
    { $or: [ { status: "A" } ,
             { age: 50 } ] }
)
```

- 条件查询 >

```sql
SELECT *
FROM people
WHERE age > 25
```

```js
db.people.find(
    { age: { $gt: 25 } }
)
```

- 条件查询 <

```sql
SELECT *
FROM people
WHERE age < 25
```

```js
db.people.find(
   { age: { $lt: 25 } }
)
```

- 复杂的条件查询

```sql
SELECT *
FROM people
WHERE age > 25
AND   age <= 50
```

```js
db.people.find(
   { age: { $gt: 25, $lte: 50 } }
)
```

- 条件查询 LIKE

```sql
SELECT *
FROM people
WHERE user_id like "%bc%"
```

```js
db.people.find( { user_id: /bc/ } )

// OR

db.people.find( { user_id: { $regex: /bc/ } } )
```

```sql
SELECT *
FROM people
WHERE user_id like "bc%"
```

```js
db.people.find( { user_id: /^bc/ } )

// OR

db.people.find( { user_id: { $regex: /^bc/ } } )
```

- 排序

```sql
SELECT *
FROM people
WHERE status = "A"
ORDER BY user_id ASC
```

```js
db.people.find( { status: "A" } ).sort( { user_id: 1 } )
```

```sql
SELECT *
FROM people
WHERE status = "A"
ORDER BY user_id DESC
```

```js
db.people.find( { status: "A" } ).sort( { user_id: -1 } )
```

- 统计数量

```sql
SELECT COUNT(*)
FROM people
```

```js
db.people.count()

// or

db.people.find().count()
```

```sql
SELECT COUNT(user_id)
FROM people
```

```js
db.people.count( { user_id: { $exists: true } } )
or
```

```js
db.people.find( { user_id: { $exists: true } } ).count()
```

```sql
SELECT COUNT(*)
FROM people
WHERE age > 30
```

```js
db.people.count( { age: { $gt: 30 } } )

// or

db.people.find( { age: { $gt: 30 } } ).count()
```

- 去除重复distinct

```sql
SELECT DISTINCT(status)
FROM people
```

```js
db.people.distinct( "status" )
```

```sql
SELECT *
FROM people
LIMIT 1
```

- 限制数量

```js
db.people.findOne()

// or

db.people.find().limit(1)
```

```sql
SELECT *
FROM people
LIMIT 5
SKIP 10
```

```js
db.people.find().limit(5).skip(10)
```

- EXPLAIN

```sql
EXPLAIN SELECT *
FROM people
WHERE status = "A"
```

```js
db.people.find( { status: "A" } ).explain()
```

## 聚合查询

上面普通查询使用`find`函数即可，但是聚合查询使用另外一个函数`aggregate`，这里是[官方文档](https://docs.mongodb.com/manual/core/aggregation-pipeline/)。

初始化数据如下，有2个表 `orders` 和 `order_lineitem` ，外键关联`order_lineitem.order_id and the orders.id `。

```json
{
  cust_id: "abc123",
  ord_date: ISODate("2012-11-02T17:04:11.102Z"),
  status: 'A',
  price: 50,
  items: [ { sku: "xxx", qty: 25, price: 1 },
           { sku: "yyy", qty: 25, price: 1 } ]
}
```
- 统计数量

```js
db.orders.aggregate( [
   {
     $group: {
        _id: null,
        count: { $sum: 1 }
     }
   }
] )
```

映射Sql语句

```sql
SELECT COUNT(*) AS count
FROM orders
```

- 计算总和

```js
db.orders.aggregate( [
   {
     $group: {
        _id: null,
        total: { $sum: "$price" }
     }
   }
] )
```

映射Sql语句

```sql
SELECT SUM(price) AS total
FROM orders
```

- 分组计算总和

```js
db.orders.aggregate( [
   {
     $group: {
        _id: "$cust_id",
        total: { $sum: "$price" }
     }
   }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       SUM(price) AS total
FROM orders
GROUP BY cust_id
```

- 分组计算总和并排序

```js
db.orders.aggregate( [
   {
     $group: {
        _id: "$cust_id",
        total: { $sum: "$price" }
     }
   },
   { $sort: { total: 1 } }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       SUM(price) AS total
FROM orders
GROUP BY cust_id
ORDER BY tota
```

- 多个字段分组

```js
db.orders.aggregate( [
   {
     $group: {
        _id: {
           cust_id: "$cust_id",
           ord_date: {
               month: { $month: "$ord_date" },
               day: { $dayOfMonth: "$ord_date" },
               year: { $year: "$ord_date"}
           }
        },
        total: { $sum: "$price" }
     }
   }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       ord_date,
       SUM(price) AS total
FROM orders
GROUP BY cust_id,
         ord_date
```

- 条件分组——HAVING

```js
db.orders.aggregate( [
   {
     $group: {
        _id: "$cust_id",
        count: { $sum: 1 }
     }
   },
   { $match: { count: { $gt: 1 } } }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       count(*)
FROM orders
GROUP BY cust_id
HAVING count(*) > 1
```

- 复杂条件分组统计

```js
db.orders.aggregate( [
   {
     $group: {
        _id: {
           cust_id: "$cust_id",
           ord_date: {
               month: { $month: "$ord_date" },
               day: { $dayOfMonth: "$ord_date" },
               year: { $year: "$ord_date"}
           }
        },
        total: { $sum: "$price" }
     }
   },
   { $match: { total: { $gt: 250 } } }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       ord_date,
       SUM(price) AS total
FROM orders
GROUP BY cust_id,
         ord_date
HAVING total > 250
```

- 复杂条件分组统计示例1

```js
db.orders.aggregate( [
   { $match: { status: 'A' } },
   {
     $group: {
        _id: "$cust_id",
        total: { $sum: "$price" }
     }
   }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       SUM(price) as total
FROM orders
WHERE status = 'A'
GROUP BY cust_id
```

- 复杂条件分组统计示例2

```js
db.orders.aggregate( [
   { $match: { status: 'A' } },
   {
     $group: {
        _id: "$cust_id",
        total: { $sum: "$price" }
     }
   },
   { $match: { total: { $gt: 250 } } }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       SUM(price) as total
FROM orders
WHERE status = 'A'
GROUP BY cust_id
HAVING total > 250
```

- 表关联

```js
db.orders.aggregate( [
   { $unwind: "$items" },
   {
     $group: {
        _id: "$cust_id",
        qty: { $sum: "$items.qty" }
     }
   }
] )
```

映射Sql语句

```sql
SELECT cust_id,
       SUM(li.qty) as qty
FROM orders o,
     order_lineitem li
WHERE li.order_id = o.id
GROUP BY cust_id
```

- 嵌套查询

```js
db.orders.aggregate( [
   {
     $group: {
        _id: {
           cust_id: "$cust_id",
           ord_date: {
               month: { $month: "$ord_date" },
               day: { $dayOfMonth: "$ord_date" },
               year: { $year: "$ord_date"}
           }
        }
     }
   },
   {
     $group: {
        _id: null,
        count: { $sum: 1 }
     }
   }
] )
```

映射Sql语句

```sql
SELECT COUNT(*)
FROM (SELECT cust_id,
             ord_date
      FROM orders
      GROUP BY cust_id,
               ord_date)
      as DerivedTable
```

## Map-Reduce

Mongo中聚合查询还有一种叫Map-Reduce，官方文档在[这里](https://docs.mongodb.com/manual/core/map-reduce/)，在思想上它跟Hadoop一样，从一个单一集合中输入数据，然后将结果输出到一个集合中。通常在使用类似SQL中`Group By`操作时，Map/Reduce会是一个好的工具。

![Map-Reduce](https://docs.mongodb.com/manual/_images/map-reduce.bakedsvg.svg)

### 接口方法定义

```js
db.collection.mapReduce(
    <map>,
    <reduce>,
    {
        out: <collection>,
        query: <document>,
        sort: <document>,
        limit: <number>,
        finalize: <function>,
        scope: <document>,
        jsMode: <boolean>,
        verbose: <boolean>,
        bypassDocumentValidation: <boolean>
    }
)
```

### 参数说明

- mapReduce： 要执行Map/Reduce集合的名字

- map: map 函数     (下面会详细介绍）

- reduce: reduce函数(下面会详细介绍）

- out: 存放结果的集合 (下面会详细介绍）

- query: 设置查询条件   <可选>

- sort: 按某个键来排序 <可选>

- limit: 指明从集合检索文档个数的最大值 <可选>

- finalize: 对reduce结果做进一步处理  <可选>

- scope: 指明通过map/reduce/finalize可以访问到的变量 <可选>

- jsMode: 指明Map/Reduce执行过程中文档保持JSON状态   <可选>

- verbose: 提供关于任务执行的统计数据  <可选>

## 示例说明

举例说明Map-Reduce的用途，虽然代码比较多，也行用上面的聚合查询，一下子就搞定了，但是这里只是举例。

比如有个订单表，如下所示，我们需要计算每个人的订单总价。

```json
{
     _id: ObjectId("50a8240b927d5d8b5891743c"),
     cust_id: "abc123",
     ord_date: new Date("Oct 04, 2012"),
     status: 'A',
     price: 25,
     items: [ { sku: "mmm", qty: 5, price: 2.5 },
              { sku: "nnn", qty: 5, price: 2.5 } ]
}
```

首先定义Map方法，就说我们后面的聚合计算需要哪些字段，由于需要计算每个人的订单总结，那么个人信息和加个肯定是我们需要的。

```js
var mapFunction1 = function() {
    emit(this.cust_id, this.price);
};
```

然后定义reduce方法，计算每个人的订单价格。

```js
var reduceFunction1 = function(keyCustId, valuesPrices) {
    return Array.sum(valuesPrices);
};
```

然后存储最后的计算结果。

```js
db.orders.mapReduce(
    mapFunction1,
    reduceFunction1,
    { out: "map_reduce_example" }
)
```

这样一个简单的Map-Reduce实例就完成了，结果放在`map_reduce_example`中。

上面示例比较简单，那么我们来一个复杂一点的例子。

一条订单记录中，有sdk的名称、数量、价格，那么要查询出日期大于`01/01/2012`，所有订单的总数，以及平均sdk价格。

首先还是定义个map函数。

```js
var mapFunction2 = function() {
    for (var idx = 0; idx < this.items.length; idx++) {
        var key = this.items[idx].sku;
        var value = {
                        count: 1,
                        qty: this.items[idx].qty
                    };
        emit(key, value);
    }
};
```

然后算出sku的数量，和总价格。

```js
var reduceFunction2 = function(keySKU, countObjVals) {
    reducedVal = { count: 0, qty: 0 };

    for (var idx = 0; idx < countObjVals.length; idx++) {
        reducedVal.count += countObjVals[idx].count;
        reducedVal.qty += countObjVals[idx].qty;
    }

    return reducedVal;
};
```

总价格出来后，还要计算出平均价格。

```js
var finalizeFunction2 = function (key, reducedVal) {
    reducedVal.avg = reducedVal.qty / reducedVal.count;
    return reducedVal;
};
```

还有日期的条件过滤，最后得出完整的map-reduce。

```js
db.orders.mapReduce(
    mapFunction2,
    reduceFunction2,
    {
        out: { merge: "map_reduce_example" },
        query: {
            ord_date:{ $gt: new Date('01/01/2012') }
        },
        finalize: finalizeFunction2
    }
)
```

## 总结

以上就是我对MongoDB的示例总结，本人是一个初学者，也有很多地方不懂，如果有错误的地方，欢迎指出。

## 相关资料

[浅谈MongoDB数据库](http://www.jianshu.com/p/9e1d34eb1d4c)

[普通查询官方文档](https://docs.mongodb.com/manual/tutorial/query-documents/)

[Sql和Mongo隐射表](https://docs.mongodb.com/manual/reference/sql-comparison/)

[聚合官方文档](https://docs.mongodb.com/manual/core/aggregation-pipeline/)

[Map-Reduce官方文档](https://docs.mongodb.com/manual/core/map-reduce/)

[Map-Reduce API](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce)