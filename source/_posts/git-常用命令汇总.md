---
title: git 常用命令汇总
date: 2016-06-03 14:26:42
tags: [git,bash]
---

### 常用命令

创建账户：
```language-git 
git config -global user.name "Your name"
git config -global user.email "you@example.com"
```
   
从仓库克隆到提交文件通用流程：
    
```language-git
 git clone git@github.com:mmrxia/project-name.git
 git add .
 git commit -m 'update file'
 git push -u origin master
```

查看当前git仓库文件状态：
```language-git
git status
```

### 其他技巧

+ 新建.gitignore文件
 
```language-git
touch .gitignore
```

+ 查看文件

```language-git
vim README.md
```

+ 查看提交历史记录
                    
```language-git
git log
```
我们常用 `-p` 选项展开显示每次提交的内容差异，用 `-2` 则仅显示最近的两次更新。
