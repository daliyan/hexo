---
title: 使用淘宝npm镜像cnpm
date: 2016-08-05 11:24:36
tags: [npm, cnpm]
---
> 在使用 node 模块时可以使用 npm install 查找 package.json 中的声明的依赖模块并安装。  

但因为国内网络原因，使用 npm 安装某些模块会非常慢，甚至无法安装所有的包。   
幸运的是,国内有几个镜像站点可以供我们使用,比如 `https://registry.cnpmjs.org`、`https://registry.npm.taobao.org`,速度非常快,镜像站会实时更新,为我们节省了好多时间.    
   
##### 那么,如何给本机换源呢?

(1)[临时]通过 `config` 配置指向国内镜像源

```
# 配置指向源
npm config set registry http://registry.cnpmjs.org
```

(2)[临时]通过 `npm` 命令指定下载源

```
npm --registry http://registry.cnpmjs.org 
```

(3)[linux]在配置文件 `~/.npmrc` 文件写入源地址

```
//打开配置文件
nano ~/.npmrc
//写入配置文件
registry =https://registry.npm.taobao.org
```

推荐使用最后一种方法,一劳永逸,前面2钟方法都是临时改变包下载源.   
如果你不想使用国内镜像站点,只需要将 写入 `~/.npmrc` 的配置内容删除即可.   


##### 使用cnpm

或者你也可以安装`cnpm`包，使用`cnpm`替代`npm`：

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

然后使用 `cnpm` 安装模块：

```
cnpm install [module-name]
```