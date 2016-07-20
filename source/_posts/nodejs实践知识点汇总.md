---
title: nodejs实践知识点汇总
date: 2016-06-03 10:21:03
tags: [nodejs]
---

### 常用命令

1 . **npm -v、node -v**： 查看npm或nodejs版本。示例：

```language-git
npm -v
```

2 . **npm init**： 会在当前目录引导创建一个package.json文件，包括名称、版本、作者这些信息等。示例：

```language-git
npm init
```

3 . **npm install**： 根据package.json文件自动安装需要的模块。示例：

```language-git
npm install
```

--save 命令，安装模块时，写入到package.json的依赖字段（dependencies）中。   
使用`--save-dev`命令，可写入到开发依赖字段（devDependencies）中。   
示例：

```language-git
npm install express --save
```

-g 将包安装到全局环境中，如：

```language-git
npm install express -g
```

4 . **npm uninstall <name>**： 卸载node模块

5 . **npm update <name>**： 更新node模块

6 . 其他

+ npm root：查看当前包的安装路径
+ npm root -g：查看全局的包的安装路径
+ npm outdated：检查包是否已经过时，此命令会列出所有已经过时的包，可以及时进行包的更新
+ npm list：查看当前目录下已安装的node包
+ npm help：查看帮助命令