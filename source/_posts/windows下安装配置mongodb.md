---
title: windows下安装配置mongodb
date: 2016-12-16 14:36:29
tags: [mongodb]
---
安装文件：官方网站 http://www.mongodb.org/downloads  
选择对应系统的文件下载。

一、解压缩文件。
  将压缩包解压，在D盘创建文件夹`mongodb`,将压缩包解压到`mongodb`文件夹中。

二、建立工作目录。
  1、建立数据存放目录  `D:\mongodb\data\db`
  2、建立日志文件  `D:\mongodb\db\log\mongodb.log`
  
三、建立配置文件。
  `mongodb`文件夹下建立`mongodb.cfg`文件，输入以下内容：
  ```language-git
  dbpath=D:\MongoDB\data #数据库路径
  logpath=D:\MongoDB\logs\mongodb.log #日志输出文件路径
  logappend=true #错误日志采用追加模式，配置这个选项后mongodb的日志会追加到现有的日志文件，而不是从新创建一个新文件
  journal=true #启用日志文件，默认启用
  quiet=true #这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
  port=27017 #端口号 默认为27017
  ```
  这里仅指定了几个常用项，更多详细配置请参考官方文档 http://docs.mongodb.org/manual/reference/configuration-options/
  
四、启动mongdb服务。
  
  至此`mongodb`文件夹下有文件夹及文件：
  ```language-git
  D:\ mongodb\bin
  D:\ mongodb\data\db
  D:\ mongodb\data\log
  ```

  配置文件和相关目录建好后使用如下方式启动mongodb：
  
  控制台进入`D:\ mongodb\bin`目录，执行以下命令:
  
  1 . 普通启动 
  ```language-git
  mongod --config D:\mongodb\mongodb.cfg
  ```
  2 . 安装为Windows服务
  ```language-git
  mongod --config D:\mongodb\mongodb.cfg --install
  ```
  注：2.6版 这种方式在win7、win8 64位版无法安装成功，其他系统未测试
  BUG链接 https://jira.mongodb.org/browse/SERVER-13515
  
  3 . 使用SC命令安装为Windows服务
   ```language-git
  sc create MongoDB binPath= "D:\mongodb\bin\mongod.exe --service --config=D:\mongodb\mongodb.cfg"
  ```
  
五、系统服务启动与删除。
```language-git
net start MongoDB  //启动MongoDB服务  
net stop MongoDB  //停止MongoDB服务   
mongod --remove //移除MongoDB服务  
```