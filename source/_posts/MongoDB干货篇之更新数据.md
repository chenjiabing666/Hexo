---
title: MongoDB干货篇之更新数据
date: 2017-05-01 08:13:32
categories: 数据库干货篇
tags: MongoDB
---

# MongoDB干货篇之数据更新

## 常用的函数
>* `update(<query>,<update>,<upsert>,<multi>)`,其中`<query>`表示筛选的条件，`<update>`是要更新的数据

>* `updateMany()`   更新所有匹配到的数据

### upsert
>`upsert`是一个布尔类型的数据，如果为`true`时，当根据`query`条件没有找到匹配的数据时，就表示插入此条数据，如果为`false`就表示不插入数据

>下面将会在一个空的集合中更新数据
```javascript
//就会插入此条数据，因为没有找到匹配的信息
db.user.update({'name':'chenjiabing','age':22,'sex':"Man"},{$set:{'hobby':'read'}},{'upsert':true});  

db.user.update({'name':'chenjiabing','age':22,'sex':"Man"},{$set:{'hobby':'read'}},true);   //和上面的语句等价

//输出  db.user.find()
{ "_id" : ObjectId("59067b70856d5893a687655f"), "age" : 22, "name" : "chenjiabing", "sex" : "Man", "hobby" : "read" }


```


### multi
>*如果这个参数为`true`,就把按条件查出来多条记录全部更新。默认为`false`,如果为`true`的话和`updateMany()`一样的效果*

>下面将会更新所有匹配到的数据

```javascript
db.user.update({name:'chenjiabing'},{$set:{'hobby':'code'}},{'multi':true});   
```







## 字段更新操作符 Field Update Operators
### $set

>`$set`用来指定一个键的值。如果这个键不存在，则创建它。**注意这里的更新默认是只更新第一条匹配到的数据，如果第一条匹配的数据已经满足修改后的条件，那么将不会执行下面匹配的信息**

>* 下面我们将会添加一条信息在数据库中

```javascript
    db.user.insert({"name":'jack',"age":22,"sex":'Man','school':{'name':'jsnu','city':'xuzhou'}});
    
```

>>运行下面的代码，将该用户的兴趣设置为“读书”并添加至文档中(此时文档中`hobby`键是不存在，该条文档就会创建它)

```javascript
db.user.update({name:'jack'},{$set:{'hobby':'read'}})
```
>>下面将会修改用户的**年龄**

```javascript
    db.user.update({'name':'jack'},{$set:{'age':20}})   
```

>>下面用`$set`修改**数据类型**，将`sex`设置为`1`

```javascript
    db.user.update({'name':'jack'},{$set:{'sex':1}})
```

>>下面用`$set`修改**内嵌文档**，必须指定文档的名字和键值

```javascript
db.user.update({name:'jack'},{$set:{'school.name':'shida','school.city':'beijing'}})
```


### $unset

>从文档中**移除**指定的键

>下面将要删除上面插入的`hobby`键

```javascript
db.user.update({name:'jack'},{$unset:{'hobby':1}})   //这里的值是任意给的，随便什么值
```

### $inc
>`$inc`修改器用来**增加**已有键的值，**或者在键不存在时创建一个键**`$inc`就是专门来增加（和减少）数字的。`$inc`只能用于**整数、长整数或双精度浮点数**。要是用在其他类型的数据上就会导致操作失败

>例如毎次有人访问该博文，该条博文的浏览数就加`1`，用键`pageViews`保存浏览数信息。这个键值上面没有定义过，所以会自动创建一个

```javascript
db.user.update({name:'jack'},{$inc:{'pageViews':1}});    //起初没有就会自动创建一个键

```

>下面演示增加和减少

```javascript
db.user.update({name:'jack'},{$inc:{'pageViews':100}})  ;  //这里是在上面的基础上加上100，此时变成了101

db.user.update({name:'jack'},{$inc:{"pageViews":-100}}) ;   //这里是在上面的基础上减去100,此时还是变成了1
```

### $rename

>**语法：**`{$rename: { <old name1>: <new name1>, <old name2>: <new name2>, ... } }`

>`$rename`操作符可以**重命名字段名称**，新的字段名称不能和文档中现有的字段名相同。

>下面重新插入一条数据，并且改变这条数据的键的名称

```javascript
db.user.insert({name:'chenjiabing','age':22,'sex':'Man','school':{'name':'jsnu','city':'beijing'}});

db.user.update({name:'chenjiabing'},{$rename:{'age':'Age'}})   //重命名age为Age

```

>下面将要演示怎样改变内嵌文档的键的名称，**注意一定要带上文档的名字**

```javascript
db.user.update({name:'chenjiabing'},{$rename:{'school.name':'school.Name','school.city':'school.City'}});
```

>如果重命名的字段字和集合中原有的字段名字相同的话就会**覆盖**原有的字段名称，那么就会造成数据的丢失

```javascript
db.user.update({name:'chenjiabing'},{'$rename':{'sex','age'}});  //这里sex变成age和原来的age相同，那么原来的age就会丢失

db.user.find({name:'chenjiabing'});  

//输出，可以看到原来的age没有了,变成了覆盖之后的

{ "_id" : ObjectId("590674ce30b9f88dd43d7ee4"), "name" : "chenjiabing", "age" : "Man", "school" : { "name" : "jsnu", "city" : "beijing" } }   
```

>**如果指定的字段不存在，那么将不会更新，对原来的字段没有影响**

```javascript
db.user.update({name:'chenjiabing'},{$rename:{value:'name'}});  //将不会有任何的改变，因为value这个字段根本不存在
```

>**`$rename`操作符也可以将子文档中键值移到其他子文档中**

```javascript
db.user.update({name:'chenjiabing'},{$rename:{'school.name':'contact.name'}});// 这里将会将school.name这个字段的值移到contact.name之中，如果contact不存在，那么就会创建一个

//输出

{ "_id" : ObjectId("590674ce30b9f88dd43d7ee4"), "name" : "chenjiabing", "age" : "Man", "school" : { "city" : "beijing" }, "contact" : { "name" : "jsnu" } }


```

## 数组更新操作符 Array Update Operators

>*只能用在键值为数组的键上的数组操作。*

### $ (query)
>**语法**:` { "<array>.$" : value }`

>当对数组字段进行更新时，且没有明确指定的元素在数组中的位置，我们使用定位操作符`$`标识一个元素，数字都是以`0`开始的。

>**注意:**
>* 定位操作符("$")作为第一个匹配查询条件的元素的占位符，也就是在数组中的索引值。
>* 数组字段必须出现查询文档中。


>向集合中插入两条数据

```javascript
db.students.insert({ "_id" : 1, "grades" : [ 78, 88, 88 ] });
db.students.insert({ "_id" : 2, "grades" : [ 88, 90, 92 ] });
```

>执行下列操作

```javascript
//查询匹配的文档中，数组有2个88，只更新第一个匹配的元素，也就是"grades.1"
db.students.update( { _id: 1, grades: 88 }, { $set: { "grades.$" : 82 } }) ;
//查询文档中没有出现grades字段，查询报错
db.students.update( { _id: 2 }, { $set: { "grades.$" : 82 } } );
```

### $push
>*如果指定的键已经存在，会向已有的数组末尾加入一个元素，要是没有就会创建一个新的数组。*

>下面我们将使用`$push`对该文档添加一条评论信息。

```javascript

//将会创建一个comments数组，因为一开始这个数组没有存在
db.user.update({name:'chenjiabing'},{$push:{comments:{'name':'jack','content':'hello thanks'}}})


//继续添加一条，在comments的末尾进行添加，此时comments变成两条数据了
db.user.update({name:'chenjiabing'},{$push:{comments:{'name':'john','content':'hello'}}})

```

### $pull
>**语法**：`db.collection.update( { field: <query> }, { $pull: { field: <query> } } );`

>*`$pull`操作符移除指定字段值为数组，且匹配`$pull`操作符移除指定字段值为数组，且匹配`$pull`语句声明的查询条件的所有元素。*

>执行如下操作

```javascript

//插入一条文档
db.profiles.insert({ votes: [ 3, 5, 6, 7, 7, 8 ] });
//移除数组中所有元素7
db.profiles.update( { votes: 3 }, { $pull: { votes: 7 } } );
//移除数组中所有大于6的元素
db.profiles.update( { votes: 3 }, { $pull: { votes: { $gt: 6 } } } );

//Result
{ votes: [ 3, 5, 6, 8 ] }

{ votes: [ 3, 5, 6 ] }


```








