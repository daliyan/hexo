---
title: javascript类型判断
date: 2016-12-22 17:50:03
tags: [javascript]
---

> JavaScript 中所有变量都是对象，除了两个例外 null 和 undefined。

理解Javascript的类型是学习Javascript的基础，但是作为新手很容易在判断Javascript上容易混淆。很容易被上面这句话误导，首先解释下上面这句话。

```language-javascript
false.toString(); // 'false'
[1, 2, 3].toString(); // '1,2,3'
(2).toString(); // '2'
```

通过上面的代码，可以看出布尔、数组、数字都具有`toString()`这一方法，其实除了`null`和`undefined`外的数据类型都继承自`Object`对象都具`Object`的方法和属性，这些看似非对象的类型使用起来却很像对象，所以也可以说它们都是对象。
可是Javascript依然具有类型，大致可以分成下面几类：

+ Number
+ String
+ Boolean
+ Object
+ Null
+ Undefined

`Number`、`String`、`Boolean`、`Null`、`Undefined`都是是基本数据类型，只有`Object`属于复杂数据类型。
`Null`和`Undefined`都表示空，它们的区别在于：`Null`表示无值，一般是人为的将变量的值设置为`null`；`Undefined`表示未知值，一般在使用`var`声明变量但未对其加以初始化时，这个变量值为`undefined`。

`Object`又分为以下几种类型：

+ Function
+ Array
+ Date
+ RegExp

#### typeof操作符
`typeof`操作符（和`instanceof`一起）或许是 JavaScript 中最大的设计缺陷， 因为几乎不可能从它们那里得到想要的结果。

```language-javascript
typeof []; // object
typeof {}; // object
typeof ''; // string
typeof new Date() // object
typeof 1; // number
typeof function () {}; // function
typeof /test/i; // object
typeof true; // boolean
typeof null; // object
typeof undefined; // undefined
```
为什么？`function`明明是`Object`类型，却显示`function`；`null`明明是`Null`类型，却显示`object`。所以`typeof`操作符对类型的判断是不靠谱的，除非类型在给定的范围且`typeof`确实能够区分这些类型。

#### 类型的区分
Javascript标准标准文档给出了区分类型的办法：

```language-javascript
Object.prototype.toString.call();
```

我们来看看上面的方法输出结果：

```language-javascript
Object.prototype.toString.call([]); // [object Array]
Object.prototype.toString.call({}); // [object Object]
Object.prototype.toString.call(''); // [object String]
Object.prototype.toString.call(new Date()); // [object Date]
Object.prototype.toString.call(1); // [object Number]
Object.prototype.toString.call(function () {}); // [object Function]
Object.prototype.toString.call(/test/i); // [object RegExp]
Object.prototype.toString.call(true); // [object Boolean]
Object.prototype.toString.call(null); // [object Null]
Object.prototype.toString.call(); // [object Undefined]
```

利用上面判断类型的方法可以写个类库，此类库来自[Axis.js](//github.com/toddmotto/axis):

```language-javascript
(function (root, factory) {
  // 判断是否使用了模块
  if (typeof define === 'function' && define.amd) {
    // 使用AMD模块
    define(factory);
  } else if (typeof exports === 'object') {
    // 使用CMD模块
    module.exports = factory;
  } else {
    // 没有使用模块，放在全局下
    root.axis = factory();
  }
})(this, function () {
  // 严格模式
  'use strict';
  var exports = {};
  // 将字符串转为数组
  var types = 'Array Object String Date RegExp Function Boolean Number Null Undefined'.split(' ');
  // 判断类型
  var type = function () {
    return Object.prototype.toString.call(this).slice(8, -1);
  };
  // 遍历types，为exports对象添加isArray、isObject...等方法
  for (var i = types.length; i--;) {
    exports['is' + types[i]] = (function (self) {
      return function (elem) {
        // type.call(elem)将type方法里的this指针指向elem
        return type.call(elem) === self;
      };
    })(types[i]);
  }
  return exports;
});
```

使用方法也很简单：

```language-javascript
axis.isArray([]); // true
axis.isObject({}); // true
axis.isString(''); // true
axis.isDate(new Date()); // true
axis.isRegExp(/test/i); // true
axis.isFunction(function () {}); // true
axis.isBoolean(true); // true
axis.isNumber(1); // true
axis.isNull(null); // true
axis.isUndefined(); // true
```

参考链接：
+ http://toddmotto.com/understanding-javascript-types-and-reliable-type-checking/
+ http://bonsaiden.github.io/JavaScript-Garden/