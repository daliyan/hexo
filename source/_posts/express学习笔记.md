---
title: express学习笔记
date: 2016-07-01 15:32:01
tags: [nodejs, express]
---

#### 1 . app.render和res.render方法的区别？

官方的api文档中是这么说的：   
> Think of app.render() as a utility function for generating rendered view strings. Internally res.render() uses app.render() to render views.

我的理解：
   
把`app.render`当成一个生成视图的工具，而且`res.render`内部也是调用了`app.render`。   
区别是这样，`app.render`只负责生成视图，你会发现它是没能力把视图响应给客户端（浏览器）的，只有`res.render`手里有`response`对象，可以把视图响应给客户端。   
`res.render`的伪代码可以看做如下: 
   
```language-javascript
res.render = function(view, locals, cb){
    app.render(view, locals, function(err, html){
        if(typeof cb !== 'undefined'){
            return cb(err, html);
        }
        res.send(html);
    });
};
```   

#### 2 . res.write和res.send区别？

> The res object represents the HTTP response that an Express app sends when it gets an HTTP request.

`res.write`和`res.send`都是发送报文到浏览器response中，并渲染在浏览器窗口。     
`res.write`使用时需要配合`res.end`方法同时使用，否则浏览器http请求会一直处于pending状态，而`res.send`单独使用即可。    

代码示例：     

```language-javascript
//res.send
app.get('/user/:id', function(req, res){
  res.send('user ' + req.params.id);
});

//res.write
app.get('/user/:id', function(req, res){
 res.status(200).write('user ' + req.params.id);
 res.end();
});
```

#### 3 . 获取request请求参数的方法？

express获取参数有三种方法：  

官网实例：   
       
> Checks route params (req.params), ex: /user/:id     
> Checks query string params (req.query), ex: ?id=12     
> Checks urlencoded body params (req.body), ex: id=    
   
+ 例如：127.0.0.1:3000/index，得到index，可以通过使用`req.params`得到；   
+ 例如：127.0.0.1:3000/index?id=12，这种情况下，这种方式是获取客户端get方式传递过来的值，通过使用`req.query.id`可以获得；     
+ 例如：127.0.0.1：300/index，然后post了一个id=2的值，这种方式是获取客户端post过来的数据，可以通过`req.body.id`获取； 
  
注：post请求，第三种方式需要以下配置：

```language-javascript
   var express        =         require("express");  
   var bodyParser     =         require("body-parser");  
   var app            =         express();  
     
   app.use(bodyParser.urlencoded({ extended: false }));   // necessary
```

#### 4 . session使用入门

`express-session`是express中比较常用的处理session的中间件,使用npm安装：

```language-git
$ npm install express-session save
```
session的认证机制必须依赖cookie，所以还应该同时安装一个`cookie-parser`，安装方法同上。然后再app.js中导入这两个中间件：

```language-javascript
var cookieParser = require('cookie-parser');
var session = require('express-session');
```

之后定义cookie解析器，注意，该定义必须写在路由分配之前：

```language-javascript
app.use(cookieParser());
app.use(session({
  secret: 'secret_string',
  name: 'name',
  cookie: {maxAge: 60000},
  resave: false,
  saveUninitialized: true,
}));
```

各参数意义：    
`secret`：用来对session数据进行加密的字符串.这个属性值为必须指定的属性。   
`name`：表示cookie的name，默认cookie的name是：connect.sid。    
`maxAge`：cookie过期时间，毫秒。     
`resave`：是指每次请求都重新设置session cookie，假设你的cookie是6000毫秒过期，每次请求都会再设置6000毫秒。     
`saveUninitialized`： 是指无论有没有session cookie，每次请求都设置个session cookie ，默认给个标示为 connect.sid。     

之后在处理请求时直接通过以下方式对session进行读写：        

```language-javascript
req.session.userinfo = userinfo;  //写入至session
res.redirect(req.session.userinfo); //从session中读取
```
 

