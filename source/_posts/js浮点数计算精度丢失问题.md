---
title: js浮点数计算精度丢失问题
date: 2016-05-11 17:22:45
tags: js 
---

 本文主要从以下3个方面来总结：
 1 . js浮点数计算精度丢失常见现象
 2 . js浮点数计算精度丢失原因
 3 . js浮点数计算精度丢失解决办法
 
 ### 一、常见现象
 
 1 . 两个简单的浮点数相加或相乘
 
 ```
 0.1 + 0.2 != 0.3 // true  0.30000000000000004
 ```
 又如：
 ```
 0.29 * 100  // 28.999999999999996
 ```
 
 2 . 大整数运算
 ```
 9999999999999999 == 10000000000000001 // true  16位和17位数竟然相等...
 ```
 又如：
 ```
 var x = 9007199254740992
 x + 1 == x // true 
 ```
 
 ### 二、原因
 计算机的二进制实现和位数限制有些数无法有限表示。就像一些无理数不能有限表示，如 圆周率 3.1415926...，1.3333... 等。JS 遵循 [IEEE 754](https://en.wikipedia.org/wiki/IEEE_floating_point) 规范，采用双精度存储（double precision），占用 64 bit。如图：
 ![](http://7xth8v.com2.z0.glb.clouddn.com/image/n_64bit.png?imageView2/2/w/800)
 
 图中的意义：
 
 + 1位用来表示符号位
 + 11位用来表示指数
 + 52位表示尾数
 
 
 浮点数，比如
 
 ```
 0.1 >> 0.0001 1001 1001 1001…（1001无限循环）
 0.2 >> 0.0011 0011 0011 0011…（0011无限循环）
 ```
 
 此时只能模仿十进制进行四舍五入了，但是二进制只有 0 和 1 两个，于是变为 0 舍 1 入。这即是计算机中部分浮点数运算时出现误差，丢失精度的根本原因。
 大整数的精度丢失和浮点数本质上是一样的，尾数位最大是 52 位，因此 JS 中能精准表示的最大整数是 Math.pow(2, 53)，十进制即 9007199254740992。
 大于 9007199254740992 的可能会丢失精度。
 
 ```
 9007199254740992     >> 10000000000000...000 // 共计 53 个 0
 9007199254740992 + 1 >> 10000000000000...001 // 中间 52 个 0
 9007199254740992 + 2 >> 10000000000000...010 // 中间 51 个 0
 ```
 实际上
 
 ```
 9007199254740992 + 1 // 丢失
 9007199254740992 + 2 // 未丢失
 9007199254740992 + 3 // 丢失
 9007199254740992 + 4 // 未丢失
 ```
 
 以上，可以知道看似有穷的数字, 在计算机的二进制表示里却是无穷的，由于存储位数限制因此存在“舍去”，精度丢失就发生了。
 
 想了解更深入的分析可以看这篇论文：[What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)
 

 
 
 ### 三、解决方案
 
 ```language-javascript
 var Calc = function () {
     /*浮点数去除小数点*/
     function toInt(n) {
         var n = +n, res = {num: n, times: 1};
         if (n !== (n | 0)) { //判断浮点数，n===parseInt(n)
             var arr = ('' + n).split('.');
             var len = arr[1].length; //小数长度
             res.times = Math.pow(10, len); //需要乘的倍数=>10的指数
             res.num = Math.round(n * res.times); //四舍五入取整
         }
         return res;
     }
 
     function operation(a, b, op) {
         var result; //最终计算的值
         var o1 = toInt(a), o2 = toInt(b);
 
         var n1 = o1.num, t1 = o1.times;
         var n2 = o2.num, t2 = o2.times;
 
         var max = Math.max(t1, t2);
 
         switch (op) {
             case 'add':
                 if (t1 > t2) {
                     result = n1 + n2 * (t1 / t2);
                 } else {
                     result = n2 + n1 * (t2 / t1);
                 }
                 result = result / max;
                 break;
             case 'subtract':
                 if (t1 > t2) {
                     result = n1 - n2 * (t1 / t2);
                 } else {
                     result = n2 - n1 * (t2 / t1);
                 }
                 result = result / max;
                 break;
             case 'multiply':
                 result = (n1 * n2) / (t1 * t2)
                 return result
                 break;
             case 'divide':
                 result = (n1 / n2) * (t2 / t1)
                 return result
                 break;
 
         }
         return result;
     }
 
     /*加*/
     function add(a, b) {
         return operation(a, b, 'add');
     }
 
     /*减*/
     function subtract(a, b) {
         return operation(a, b, 'subtract');
     }
 
     /*乘*/
     function multiply(a, b) {
         return operation(a, b, 'multiply');
     }
 
     /*除*/
     function divide(a, b) {
         return operation(a, b, 'divide');
     }
 
     //exports
     return {
         add: add,
         subtract: subtract,
         multiply: multiply,
         divide: divide
     }
 }(); 
 ```
 
 ### 参考资料
 http://0.30000000000000004.com
 http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html