---
title: MongoDB干货篇之查询数据
date: 2017-04-30 14:53:00
categories: 数据库干货篇
tags: MongoDB
---
# MongoDB干货篇之查询
## 准备工作
>**在开始之前我们应该先准备数据方便演示，这里我插入的了几条数据，数据如下：**
```javascript
    db.user.insertMany(
    [{
    name:'jack',
    age:22,
    sex:'Man',
    tags:['python','c++','c'],
    grades:[22,33,44,55],
    school:{
    name:'shida',
    city:'xuzhou'
    }
    },{
    name:'jhon',
    age:33,
    sex:null,
    tags:['python','java'],
    grades:[66,22,44,88],
    school:{
    name:'kuangda',
    city:'xuzhou'
    }
    },
    {
    name:'xiaoming',
    age:33,
    tags:['python','java'],
    grades:[66,22,44,88],
    school:{
    name:'kuangda',
    city:'xuzhou'
    }
    }
    ]
    )
```

## find()
>**其中`query`表示查找的条件，相当于`mysql`中`where`子句,`projection`列出你想要查找的数据，格式为`db.collection.find(find(<query filter>, <projection>))`**

### 实例：

>* **下面不带参数的查找，将会查找出所有的结果**
```javascript=
    db.find().pretty();
    
    //输出结果
    
    
{                                                     
        "_id" : ObjectId("59056f81299fe049404b2899"), 
        "name" : "jack",                              
        "age" : 22,                                   
        "tags" : [                                    
                "python",                             
                "c++",                                
                "c"                                   
        ],                                            
        "grades" : [                                  
                22,                                   
                33,                                   
                44,                                   
                55                                    
        ],                                            
        "school" : {                                  
                "name" : "shida",                     
                "city" : "xuzhou"                     
        }                                             
}                                                     
                                                    
    
    
```

>* **下面找出满足`name`为`jack`的数据，并且只输出`name`,`age`,这里的`_id`是默认输出的，如果不想输出将将它设置为`0`，想要输出那个字段将它设置为1**

```javascript
    db.user.find({name:'jack'},{name:1,age:1})
    
    //输出结果
    { "_id" : ObjectId("59056f81299fe049404b2899"), "name" : "jack", "age" : 22 }
    
    
    db.user.find({name:'jack'},{name:1,age:1，_id:0})
    
    //输出结果
    {"name" : "jack", "age" : 22 }
    

```
>>**注意这里的一个 `projection`不能 同时 指定包括和排除字段，除了排除 `_id `字段。 在 显式包括 字段的映射中，`_id` 字段是唯一一个您可以 显式排除 的。

## 查询内嵌文档
>**上述例子中插入的`school`数据就表示内嵌文档**

### 完全匹配查询
>**完全匹配查询表示`school`中的查询数组必须和插入的数组完全一样，顺序都必须一样才能查找出来**

```javascript
 db.user.find({name:'jack',school:{name:'shida',city:'xuzhou'}});
 
 //输出结果
 
 { "_id" : ObjectId("59056f81299fe049404b2899"), "name" : "jack", "age" : 22, "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }
 
 
 //下面是指定输出的字段，这里的school.name表示只输出school文档中name字段，必须加引号
 db.user.find({name:'jack',school:{name:'shida',city:'xuzhou'}},{name:1,age:1,'school.name':1});
 //输出结果
 { "_id" : ObjectId("59056f81299fe049404b2899"), "name" : "jack", "age" : 22, "school" : { "name" : "shida" } }
 
```

### 键值对查询
>**可以通过键值对查询，不用考虑顺序，比如`'school.name':'shida'`，表示查询学校名字为`shida`的数据，这里的引号是必须要的**

```javascript
    db.user.find({'school.name':'shida'},{name:1,school:1});
    
    //输出结果
    
    { "_id" : ObjectId("59056f81299fe049404b2899"), "name" : "jack", "school" : { "name" : "shida", "city" : "xuzhou" } }

```

## 查询操作符
>**下面我们将配合查询操作符来执行复杂的查询操作，比如元素查询、 逻辑查询 、比较查询操作。我们使用下面的比较操作符`"$gt"` 、`"$gte"`、 `"$lt"`、 `"$lte"`(分别对应`">"`、 `">="` 、`"<"` 、`"<="`)**

### 实例
>**下面查询年龄在`20-30`之间的信息**

```javascript
db.user.find({
age:{$gt:20,$lt:30}  
})

//输出
{ "_id" : ObjectId("59056f81299fe049404b2899"), "name" : "jack", "age" : 22, "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }
```

### $ne
>**`$ne`表示不相等，例如查询年龄不等于`22`岁的信息**

```javascript
db.user.find({age:{$ne:22}})

//输出
{ "_id" : ObjectId("59057c16f551d8c9003d31e0"), "name" : "jhon", "age" : 33, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }

```

### slice
>**`$slice`操作符控制查询返回的数组中元素的个数。此操作符根据参数`{ field: value }` 指定键名和键值选择出文档集合，并且该文档集合中指定`array`键将返回从指定数量的元素。如果`count`的值大于数组中元素的数量，该查询返回数组中的所有元素的。**

>**语法：`db.collection.find( { field: value }, { array: {$slice: count }})`;**

>* **下面将查询`grades`中的前两个数**

```javascript
db.user.find({name:'jack'},{grades:{$slice:2},name:1,age:1,'school.name':1});

//输出，可以看出这里的grades只输出了前面两个

{ "_id" : ObjectId("59057c16f551d8c9003d31df"), "name" : "jack", "age" : 22, "grades" : [ 22, 33 ], "school" : { "name" : "shida" } }
```

>* **下面将输出后3个数据**

```javascript
db.user.find({name:'jhon'},{grades:{$slice:-3},name:1});

//输出
{ "_id" : ObjectId("59057c16f551d8c9003d31e0"), "name" : "jhon", "grades" : [ 22, 44, 88 ] }

```

>* **下面介绍指定一个数组作为参数。数组参数使用`[ skip , limit ]` 格式，其中第一个值表示在数组中跳过的项目数,第二个值表示返回的项目数。**

```javascript
db.user.find({name:'jack'},{grades:{$slice:[2,2]},name:1});  //这里将会跳过前面的两个，直接得到后面的两个数据


//输出

{ "_id" : ObjectId("59057c16f551d8c9003d31df"), "name" : "jack", "grades" : [ 44, 55 ] }
```

### $exists

>**如果`$exists`的值为`true`,选择存在该字段的文档,若值为`false`则选择不包含该字段的文档**

>**下面将会查询不存在sex这一项的信息**

```javascript
db.user.find({sex:{$exists:false}})

//结果
{ "_id" : ObjectId("59058460fe58ed1089f2a5cd"), "name" : "xiaoming", "age" : 33, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }


db.user.find({sex:{$exists:true}});

//结果
{ "_id" : ObjectId("59058460fe58ed1089f2a5cb"), "name" : "jack", "age" : 22, "sex" : "Man", "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }
{ "_id" : ObjectId("59058460fe58ed1089f2a5cc"), "name" : "jhon", "age" : 33, "sex" : null, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }

```

### $or
>**执行逻辑`OR`运算,指定一个至少包含两个表达式的数组，选择出至少满足数组中一条表达式的文档。**
>**语法：`{ $or: [ { <expression1> }, { <expression2> }, ... , { <expressionN> } ] }`**

>* **下面将要查找`age`等于`22`或者`age`等于`33`的值**

```javascript
db.user.find({$or:[{age:22},{age:33}]})

//结果

{ "_id" : ObjectId("59058460fe58ed1089f2a5cb"), "name" : "jack", "age" : 22, "sex" : "Man", "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }
{ "_id" : ObjectId("59058460fe58ed1089f2a5cc"), "name" : "jhon", "age" : 33, "sex" : null, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }
{ "_id" : ObjectId("59058460fe58ed1089f2a5cd"), "name" : "xiaoming", "age" : 33, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }

```

>* **下面将会查找出年龄为22或者33并且姓名为`jack`的人的信息**

```javascript
db.user.find({name:'jack',$or:[{age:33},{age:22}]})

//结果

{ "_id" : ObjectId("59058460fe58ed1089f2a5cb"), "name" : "jack", "age" : 22, "sex" : "Man", "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }

```

### $and
>**指定一个至少包含两个表达式的数组，选择出满足该数组中所有表达式的文档。`$and`操作符使用短路操作，若第一个表达式的值为“`false`”,余下的表达式将不会执行。**
>**语法：`{ $and: [ { <expression1> }, { <expression2> } , ... , { <expressionN> } ] }`**

>* **下面将会查找年龄在`20-30`之间的信息，对于下面使用逗号分隔符的表达式列表，`MongoDB`会提供一个隐式的`$and`操作：**

```javascript
db.user.find({$and:[{age:{$gt:20}},{age:{$lt:30}}]})
//上述语句相当于db.user.find({age:{$gt:20},age:{$lt:30}})

//结果
{ "_id" : ObjectId("59058460fe58ed1089f2a5cb"), "name" : "jack", "age" : 22, "sex" : "Man", "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }

```

### $in
>**匹配键值等于指定数组中任意值的文档。类似`sql`中`in`，只要匹配一个`value`就会输出**
>**语法：`{ field: { $in: [<value1>, <value2>, ... <valueN> ] } }`**

>* **下面将会查找grades中存在22,33之间的任意一个数的信息**

```javascript
 db.user.find({grades:{$in:[22,33]}})
 
 //输出
 
{ "_id" : ObjectId("59058460fe58ed1089f2a5cb"), "name" : "jack", "age" : 22, "sex" : "Man", "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }
{ "_id" : ObjectId("59058460fe58ed1089f2a5cc"), "name" : "jhon", "age" : 33, "sex" : null, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }
{ "_id" : ObjectId("59058460fe58ed1089f2a5cd"), "name" : "xiaoming", "age" : 33, "tags" : [ "python", "java" ], "grades" : [ 66, 22, 44, 88 ], "school" : { "name" : "kuangda", "city" : "xuzhou" } }
```

### $nin
>**　匹配键不存在或者键值不等于指定数组的任意值的文档。类似`sql`中`not in`(SQL中字段不存在使用会有语法错误).**

>* **查询出`grades`中不存在100或者44的文档**

```javascript
db.user.find({grades:{$nin:[100,44]}})
```

### $not
>**执行逻辑`NOT`运算，选择出不能匹配表达式的文档 ，包括没有指定键的文档。`$not`操作符不能独立使用，必须跟其他操作一起使用**

>**语法:{ field: { $not: { <operator-expression> } } }**

>* **查询年龄不大于30的信息**

```javascript
db.user.find({age:{$not:{$gt:30}}})

//输出
{ "_id" : ObjectId("59058460fe58ed1089f2a5cb"), "name" : "jack", "age" : 22, "sex" : "Man", "tags" : [ "python", "c++", "c" ], "grades" : [ 22, 33, 44, 55 ], "school" : { "name" : "shida", "city" : "xuzhou" } }

```

## 迭代游标的查询
>**学过高级语言的朋友都知道迭代的问题，像java,下面使用迭代的方法查询**

```javascript
    var cursor=db.usr.find();
    
    //这里使用迭代输出所有的数据
    while(cursor.hasNext())    //这里的hasNext()是判断是否下一个中还有可迭代的值，如果没有返回false
    {
        printjson(cursor.next());     //这里的cursor.next是迭代的输出，printjson是代替print(tojson()) 
    }
    
    
    print cursor.count()    //输出其中有多少个数据
    
    cursor.forEach(printjson);   //forEach输出
    
    
    var document=cursor.toArray();     //将迭代对象转换成数组
    
    print document[0];       //以数组的形式输出
     
```









