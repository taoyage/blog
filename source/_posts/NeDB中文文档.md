---
title: NeDB中文文档
date: 2016-09-29 23:41:15
categories: 后台开发
tags: 
  - node嵌入式数据库
  - NeDB中文文档
  - NeDB中文API
  - NeDB教程
---


## NeDB简介

NeDB是一款用于Node.js开发的NoSQL嵌入式内存数据库,完全采用javascript开发,可以使用在html5桌面应用程序,以及浏览器端应用,其api是MongoDB的一个子集,具有简单,轻量,速度快等特点.

## 安装NeDB

NeDB可以通过npm和bower来进行安装

```
$ npm install nedb --save

$ bower install nedb --save
```

## NeDB API

### 创建/加载一个数据库

```
// 类型 1: 创建一个内存数据库 (不需要去加载数据库)
var Datastore = require('nedb')
var db = new Datastore();


// 类型 2: 本地存储数据库,需要手动去加载数据库
var Datastore = require('nedb')
var db = new Datastore({ filename: 'path/to/datafile' });

db.loadDatabase(); //数据库可以直接加载或者用下面这种回调的方式
db.loadDatabase(function (err) { 
	
});


// 类型 3: 本地存储数据,自动加载数据库
var Datastore = require('nedb')
  , db = new Datastore({ filename: 'path/to/datafile', autoload: true });


// 类型 4: 本地存储数据用于html5和javascript来制作的桌面应用程序去调用'nwtest'
// 举例,在linux上, data文件在 ~/.config/nwtest/nedb-data/something.db
var Datastore = require('nedb')
  , path = require('path')
  , db = new Datastore({ filename: path.join(require('nw.gui').App.dataPath, 'something.db') });


// 类型 5: 创建多个数据库
db = {};
db.users = new Datastore('path/to/users.db');
db.robots = new Datastore('path/to/robots.db');

// 如果不配置autoload,这里需要手动加载数据库(该方法是异步的)
db.users.loadDatabase();
db.robots.loadDatabase();
```

### 插入数据(db.inster(doc,callback))

数据类型可以是String,Number,Boolean,Date,null,也可以使用数组和对象来进行存储.如果字段是undefined,将不会进行存储(这一点和MongoDB有点不同,MongoDB会吧undefined自动转换成null来进行存储).

如果文档中没有id字段,NeDB将自动生成一个16位字符的字符串存储到数据库,无法修改.

字段名不能以'$'开头或包含'.'

```
var doc = { 
	hello: 'world',
	n: 5, 
	today: new Date(),
	nedbIsAwesome: true,
	notthere: null,
	notToBeSaved: undefined,  //undefined值将不会进行存储
	fruits: [ 'apple', 'orange', 'pear' ],
	infos: { name: 'nedb' }
};

db.insert(doc, function (err, newDoc) {  
	// Callback
});

//callback函数可有可无也可以写成下面这种形式

db.insert(doc)

```

### 插入多个数组

```
db.insert([{ a: 5 }, { a: 42 }], function (err, newDocs) {
 	
});

// 以下的例子,两个a字段的值相同将会报错
db.insert([{ a: 5 }, { a: 42 }, { a: 5 }], function (err) {

});
```

### 数据库

这里拟定了一个数据库,下面所介绍的内容,都会以这个数据库进行操作,举例

```
{ _id: 'id1', planet: 'Mars', system: 'solar', inhabited: false, satellites: ['Phobos', 'Deimos'] }
{ _id: 'id2', planet: 'Earth', system: 'solar', inhabited: true, humans: { genders: 2, eyes: true } }
{ _id: 'id3', planet: 'Jupiter', system: 'solar', inhabited: false }
{ _id: 'id4', planet: 'Omicron Persei 8', system: 'futurama', inhabited: true, humans: { genders: 7 } }
{ _id: 'id5', completeData: { planets: [ { name: 'Earth', number: 3 }, { name: 'Mars', number: 2 }, { name: 'Pluton', number: 9 } ] } }
```



### 查找文档(db.find({query},callback))

在对文档进行匹配查询的时候,可以去使用($lt, $lte, $gt, $gte, $in, $nin, $ne)比较运算符,也可以去使用($or, $and, $not , $where)逻辑运算符,以及正则表达式.

callback(可选): 包含参数err和docs，err里是错误信息，docs是查询到的文档.

#### 基本查询

```
// 查找所有带有solar的数据
db.find({ system: 'solar' }, function (err, docs) {
  // 这里的docs返回的将id1,id2,id3文档的数组
  // 如果没有查询到含有solar值的system字段,docs返回的将是一个[]
});

// 使用正则表达式去匹配查询含有/ar/值的planet字段文档
db.find({ planet: /ar/ }, function (err, docs) {
  // 返回的docs将是包含mars,earth值的文档
});

// 使用多个条件去查询
db.find({ system: 'solar', inhabited: true }, function (err, docs) {
  // 这里的docs返回的将是只包含Earth值的文档
});

// 使用对象的匹配查找
db.find({ "humans.genders": 2 }, function (err, docs) {
  // docs返回的将是包含Earth值的文档
});

// 通过数组对象去匹配查询
db.find({ "completeData.planets.name": "Mars" }, function (err, docs) {
  // 这里将返回id5的文档
});

db.find({ "completeData.planets.name": "Jupiter" }, function (err, docs) {
  // 返回空数组
});

db.find({ "completeData.planets.0.name": "Earth" }, function (err, docs) {
  // 返回id5的文档
});


// 对象深度比较查询，不要与"."的使用混淆
db.find({ humans: { genders: 2 } }, function (err, docs) {
  // 返回空, 因为 { genders: 2 } 不等于 { genders: 2, eyes: true }
});

// 查询匹配所有文档
db.find({}, function (err, docs) {
});

// 如果只想查找某一条文档,可以使用唯一的id来进行查询
db.findOne({ _id: 'id1' }, function (err, doc) {
  // 如果没有匹配的文档,返回的将是null
});
```

#### 使用比较运算符查询($lt, $lte, $gt, $gte, $in, $nin, $ne, $exists, $regex)

语法是 `{ field: { $op: value } }` 这里的 `$op` 是表示这里可以填写任意的运算符

- $lt, $lte: 小于，小于等于
- $gt, $gte: 大于，大于等于
- $in: 属于
- $ne, $nin: 不等于，不属于
- $exists: 取值为true或者false，用于检测文档是否具有某一字段
- $regex: 检测字符串是否与正则表达式相匹配

```
// $lt, $lte, $gt , $gte 只能用于数字和字符串
db.find({ "humans.genders": { $gt: 5 } }, function (err, docs) {
  // 这里doc返回将是一个humans.genders值大于5的文档
});

// 当使用字符串的时候,将使用字典里字母的顺序进行匹配查找
db.find({ planet: { $gt: 'Mercury' }}, function (err, docs) {
  // 返回的是包含Omicron Persei 8值的文档
})

// $in和$nin 的使用方式相同
db.find({ planet: { $in: ['Earth', 'Jupiter'] }}, function (err, docs) {
  // 返回含有 Earth 和 Jupiter 值的文档
});

// 使用 $exists
db.find({ satellites: { $exists: true } }, function (err, docs) {
  // docs contains only Mars
  // 返回含有Mars值的文档
});

// 同时使用正则和其他匹配方式进行匹配
db.find({ planet: { $regex: /ar/, $nin: ['Jupiter', 'Earth'] } }, function (err, docs) {
  // docs only contains Mars because Earth was excluded from the match by $nin
  // 返回结果应该只有包含Mars值的文档,因为$nin匹配方式排除了含有Earth值的文档
});
```

#### 使用数组字段查询

当你的文档里有一个字段是数组时,NeDB首先会尝试去看是不是一个数组,如果是的话会进行精确匹配查找,然后去判断此数组是否使用了具体的比较运算操作方法(这里只支持`$size`和`$elemMatch`),如果没有,会对其所有元素进行匹配.

- $size 匹配数组长度
- $$elemMatch 如果对象有一个元素是数组,匹配数组内的元素 

```
// 精确匹配
db.find({ satellites: ['Phobos', 'Deimos'] }, function (err, docs) {
  // 返回的是含有Mars值的文档
})
db.find({ satellites: ['Deimos', 'Phobos'] }, function (err, docs) {
  // 返回空
})

// 使用具体的比较方法
// 使用$elemMatch进行数组匹配,所有的条件都要匹配上才行
db.find({ completeData: { planets: { $elemMatch: { name: 'Earth', number: 3 } } } }, function (err, docs) {
  // 这里返回的是包含id5的文档
});

db.find({ completeData: { planets: { $elemMatch: { name: 'Earth', number: 5 } } } }, function (err, docs) {
  // 这里返回空
});

// 你可以在$elemMatch中使用任何比较运算操作
db.find({ completeData: { planets: { $elemMatch: { name: 'Earth', number: { $gt: 2 } } } } }, function (err, docs) {
  // 返回含有id5的文档
});

// 注意不能使用嵌套的运算符, e.g. { $size: { $lt: 5 } } 将会发生错误
db.find({ satellites: { $size: 2 } }, function (err, docs) {
  // 返回含有mars值的文档
});

db.find({ satellites: { $size: 1 } }, function (err, docs) {
  // 返回空数组
});

// 如果文档的字段是一个数组,匹配数组中的任意一个字段
db.find({ satellites: 'Phobos' }, function (err, docs) {
  // 返回mars值的文档
});

```

#### 使用逻辑运算符$or, $and, $not, $where

你可以结合逻辑运算符去查询
- $or,$and 或和与 
- $no 非
- $where 指定条件



暂时还没有写完

此文档由本人自己翻译,如果有错误或者翻译不对的地方请留下建议,万分感谢,如有转载者请注明,并加上链接

此文章原文链接:[http://www.taoyage.net/2016/09/29/NeDB中文文档](http://www.taoyage.net/2016/09/29/NeDB中文文档)

此文档原英文文档链接:[https://github.com/louischatriot/nedb#creatingloading-a-database](https://github.com/louischatriot/nedb#creatingloading-a-database)





















