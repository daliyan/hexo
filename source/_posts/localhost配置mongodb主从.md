---
title: localhost配置mongodb主从   
date: 2017-03-01 11:40:38   
tags: [mongodb]   
---

#### 一、mongodb安装配置   

这部分可参考博文 [windows下安装配置mongodb](http://blog.xiaqingsong.com/2016/12/16/windows%E4%B8%8B%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEmongodb/)

#### 二、 主从配置

> 在生产环境中单台数据库一般不能满足业务稳定性的需求，所以数据库主从复制架构在生产环境中很常见，用于主从复制也是Mongodb最常用的一种架构方式。   
这种方式非常灵活，可用于备份、故障恢复、读扩展等,从而提高数据处理性能和冗余，常用架构模式是一主一从、一主多从、双主。

本着实验目的，我们今天简单实现一主一从的配置。

注：主从服务器我们指定端口分别为 `27017` 和 `27019`。  

根目录下我们分别新建`mongodb.cfg`和`mongodb.slave.cfg`主从启动配置文件，详细配置如下：

**master**(mongodb.cfg): 

```language-git
dbpath=D:\mongodb\data\db\master #规定数据库的位置
logpath=D:\mongodb\data\log\master\mongodb.log #规定数据库的日志文件

port=27017 #指定端口
#fork=true #后台运行

master=true #设置主

# bind_ip=127.0.0.1,192.168.0.4 #允许的地址 为了安全
nohttpinterface=true #禁止http访问
```
  
**slave**(mongodb.slave.cfg): 

```language-git
dbpath=D:\mongodb\data\db\slave #规定数据库的位置
logpath=D:\mongodb\data\log\slave\mongodb.log #规定数据库的日志文件

port=27019 #指定端口
#fork=true #后台运行

slave=true #声明从
source=127.0.0.1:27017 #规定从属于哪个ip  注意：ip是主服务器的  最好用内网ip

# bind_ip=127.0.0.1,192.168.0.4 #允许的地址 为了安全
nohttpinterface=true #禁止http访问
```

#### 三、启动

进入`bin`目录，执行下面步骤：

```language-git
mongod -f ../mongodb.cfg  #启动主服务器
```

从服务器启动，同理如上：
       
```language-git
mongod -f ../mongodb.slave.cfg  #启动从服务器    
```

启动完成后，我们分别另起一个窗口，进入数据库：

```
mongo --host localhost --port 27017  #进入主数据库 
mongo --host localhost --port 27019  #进入主数据库 
```

之后，我们在从服务器执行：

```
rs.slaveOk();
show dbs;  #如果出现主数据库的数据，说明同步成功
```

详细执行效果，可参考下图所示：

![](http://7xth8v.com2.z0.glb.qiniucdn.com/image/n_mongodb%20master_and_slave_test.png)