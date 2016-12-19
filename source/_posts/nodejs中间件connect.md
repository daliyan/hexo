---
title: nodejs中间件connect
date: 2016-12-19 11:19:21
tags: [nodejs]
---

#### 什么是connect？
Connect 是Node.js中的一个模块，可以用来创建中间件的一个框架，它自身已经包装了Node的HTTP模块的Server以及Server的req和res的对象。
它干的活其实就是处理请求，然后响应客户端或是让下一个中间件继续处理，它的原型是这个样子的:

```language-javascript
function (req, res, next) {
  // 中间件
}
```

当next回调函数被调用，中间件就完成工作并传递到下一个中间件，如果没其他中间件则进入到应用逻辑继续执行。

`logger`: 用户请求日志中间件
`csrf`: 跨域请求伪造保护中间件
`compress`: gzip压缩中间件
`basicAuth`: basic认证中间件
`bodyParser`: 请求内容解析中间件
`json`: JSON解析中间件
`urlencoded`: application/x-www-form-urlencode请求解析中间件
`multipart`: multipart/form-data请求解析中间件
`timeout`: 请求超时中间件
`cookieParser`: cookie解析中间件
`session`: 会话管理中间件
`cookieSession`: 基于cookies的会话中间件
`methodOverride`: HTTP伪造中间件
`reponseTime`: 计算响应时间中间件
`staticCache`: 缓存中间件
`static`: 静态文件处理中间件
`directory`: 目录列表中间件
`vhost`: 虚拟二级域名映射中间件
`favicon`: 网页图标中间件
`limit`: 请求内容大小限制中间件
`query`: URL解析中间件
`errorHadnler`: 错误处理中间件

参考: http://blog.fens.me/nodejs-connect/