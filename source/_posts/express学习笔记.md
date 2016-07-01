---
title: express学习笔记
date: 2016-07-01 15:32:01
tags: [nodejs, express]
---

#### 1. app.render和res.render方法的区别？

官方的api文档中是这么说的：   
> Think of app.render() as a utility function for generating rendered view strings. Internally res.render() uses app.render() to render views.

我的理解：
   
把`app.render`当成一个生成视图的工具，而且`res.render`内部也是调用了`app.render`。   
区别是这样，`app.render`只负责生成视图，你会发现它是没能力把视图响应给客户端（浏览器）的，只有`res.render`手里有`response`对象，可以把视图响应给客户端。   
`res.render`的伪代码可以看做如下: 
   
```javascript
res.render = function(view, locals, cb){
    app.render(view, locals, function(err, html){
        if(typeof cb !== 'undefined'){
            return cb(err, html);
        }
        res.send(html);
    });
};
```   

#### 2. res.write和res.send区别？

> The res object represents the HTTP response that an Express app sends when it gets an HTTP request.

`res.write`和`res.send`都是发送报文到浏览器response中，并渲染在浏览器窗口。     
`res.write`使用时需要配合`res.end`方法同时使用，否则浏览器http请求会一直处于pending状态，而`res.send`单独使用即可。    

代码示例：     

```javascript
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

