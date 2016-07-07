---
title: centOS下安装nodeJS
date: 2016-07-04 14:30:07
tags: [nodejs, Linux]
---

#### 一、源码安装

1 . 安装gcc-c编译器
  
```language-bash
[root@localhost ~]# sudo yum install gcc gcc-c++
```

2 . 现在nodejs源码并解压  

```language-bash
[root@localhost ~]# wget https://nodejs.org/dist/v4.4.7/node-v4.4.7-linux-x86.tar.gz
[root@localhost ~]# tar xvf node-v4.4.7-linux-x86
```

3 . 编译

```language-bash
[root@localhost ~]# ./configure
[root@localhost ~]# make && make install
```


#### 二、nvm安装

1 . 获取nvm

``` language-bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.31.2/install.sh | bash
```

2 . 安装nodejs
 
 可用 `nvm ls-remote` 列出所有可安装的node版本，然后安装需要的版本。
 ```language-bash
 nvm install v4.4.7
 ```
 

安装完成后可查看node版本。

```language-bash
node -v
```
 
 
 