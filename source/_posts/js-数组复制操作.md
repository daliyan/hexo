---
title: js 数组复制操作
date: 2016-05-22 11:31:55
tags:
---

js中数组和对象，使用“=”号赋值的时候，由于是引用类型，只是改变了引用对象的指针指向，改变值时会引起连锁反应。
简单效果如下：
![](http://note.youdao.com/yws/public/resource/f19fdadbf6e08dd9e8815d00bcfc0a79/FE74C0130EAF4F01832176D552AC5300)

避免指针指向的方法主要有一下几种：
（1）使用slice()方法。slice() 函数可从已有的数组中返回选定的元素；语法为：arr.slice(start,end);
slice()函数将会返回一个新的数组对象，所以使用slice(0)或slice()就可以实现数组的复制。如下：
![](http://note.youdao.com/yws/public/resource/f19fdadbf6e08dd9e8815d00bcfc0a79/88580C670A80470E82F454FD03BE561E) 

（2）使用concat()方法。语法为：arr.concat(arr1,arr2,arr3,...,arrn);
concat()用于多个数组的合并，但是返回的结果是一个新的数组，而不再引用用于合并的任何一个数组。如下：
![](http://note.youdao.com/yws/public/resource/f19fdadbf6e08dd9e8815d00bcfc0a79/9DEAB14934984A95BE34101BBD3D310D)

（3）笨方法：使用for循环；原理就不说了，直接上代码：
 ![](http://note.youdao.com/yws/public/resource/f19fdadbf6e08dd9e8815d00bcfc0a79/F639B6D879244986B6E101963F9AC5EB)

（4）使用jQuery的extend或merge方法：
$.extend 功能很强大，可以合并数组，对象！具体可参见jQuery api。
 ![](http://note.youdao.com/yws/public/resource/f19fdadbf6e08dd9e8815d00bcfc0a79/087F389726594492B32B0E0A41D7DC3D)

$.merge 只能合并一维数组，且一次只能合并2个，个人觉得没有concat方法强大。
![](http://note.youdao.com/yws/public/resource/f19fdadbf6e08dd9e8815d00bcfc0a79/E26F1DB0A99B438D9B10E88E89A95935)


其他参考：
1. javascript 原始类型和引用类型


