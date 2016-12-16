---
title: mongoose学习笔记
date: 2016-12-16 16:54:48
tags: [mongodb]
---

## 一、快速通道

### 1. 名词解释
Schema（模式）: 一种以文件形式存储的数据库模型骨架，不具备数据库的操作能力。 
Model（模型）: 源于Schema生成的模型，具有抽象属性和行为的数据库操作对。
Entity（实体）: 源于Model创建的实体，操作可影响数据库。

  > 注意：
  1 . 本学习文档采用严格命名方式来区别不同对象，例如：
  ```language-javascript
  var PersonSchema;   //Person的文本属性
  var PersonModel;    //Person的数据库模型
  var PersonEntity;   //Person实体
  ```
  2 . Schema --> Model，Model --> Entity。Model和Entity都可对数据库操作造成影响，但Model比Entity更具操作性。
  
  
### 2. 准备工作
1 . 首先你必须安装MongoDB和NodeJS
  mongodb安装可参考： [windows下安装配置mongodb](http://blog.xiaqingsong.com/2016/12/16/windows%E4%B8%8B%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEmongodb/)

2 . 在项目只能够创建一个数据库连接，如下:

```language-javascript
var mongoose = require('mongoose');    //引用mongoose模块
var db = mongoose.createConnection('localhost','test'); //创建一个数据库连接
```

3 . 打开本机localhost的test数据库时，我们可以监测是否有异常

```language-javascript
db.on('error',console.error.bind(console,'连接错误:'));
db.once('open',function(){
  //mongodb connected!
});
```

  > 注意：
  成功开启数据库后，就可以执行数据库相应操作，假设以下代码都在回调中处理

4 . 定义一个Schema

```language-javascript
var PersonSchema = new mongoose.Schema({
  name:String   //定义一个属性name，类型为String
});
```

5 . 将该Schema发布为Model

```language-javascript
var PersonModel = db.model('Person',PersonSchema);
//如果该Model已经发布，则可以直接通过名字索引到，如下：
//var PersonModel = db.model('Person');
//如果没有发布，上一段代码将会异常
```

6 . 用Model创建Entity

```language-javascript
var personEntity = new PersonModel({name:'Krouky'});
//打印这个实体的名字看看
console.log(personEntity.name); //Krouky
```

7 . 我们甚至可以为此Schema创建方法

```language-javascript
//为Schema模型追加speak方法
PersonSchema.methos.speak = function(){
  console.log('我的名字叫'+this.name);
}
var PersonModel = db.model('Person',PersonSchema);
var personEntity = new PersonModel({name:'Krouky'});
personEntity.speak();//我的名字叫Krouky
```

8 . Entity是具有具体的数据库操作CRUD的

```language-javascript
personEntity.save();  //执行完成后，数据库就有该数据了
```

9 . 如果要执行查询，需要依赖Model，当然Entity也是可以做到的

```language-javascript
PersonModel.find(function(err,persons){
  //查询到的所有person
});
```

  > 注意：
  1 . 具体的如何配置Schema、Model以及Model和Entity的相关操作，我们会在后面进行
  2 . Model和Entity都有能影响数据库的操作，但仍有区别，后面我们也会做解释