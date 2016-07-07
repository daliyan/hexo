---
title: centOS下配置git
date: 2016-07-04 16:14:54
tags: [git, Linux]
---

#### 1. 安装git

```language-bash
[root@localhost ~]# yum install git
```

#### 2. 配置git

1 . 进入 `.ssh` 目录

```language-bash
[root@localhost ~]# cd .ssh/
```

2 . 生成ssh-keygen

```language-bash
[root@localhost .ssh]# ssh-keygen -t rsa -C 'this.xqs@gmail.com'
```

会出现以下提示：

Generating public/private rsa key pair.    
Enter file in which to save the key (/root/.ssh/id_rsa): （可不填）    
Enter passphrase (empty for no passphrase):  （可不填）    
Enter same passphrase again:  （可不填）    
Your identification has been saved in /root/.ssh/id_rsa.     
Your public key has been saved in /root/.ssh/id_rsa.pub.     
The key fingerprint is:        
8f:8e:89:00:1a:3e:83:d1:95:fc:e8:9c:22:85:4c:a7 this.xqs@gmail.com        
    
3 . 复制 `id_rsa.pub` 文件中的内容，粘贴到github新增的ssh keys中。

```language-bash
[root@localhost .ssh]# ls
// id_rsa  id_rsa.pub
   
[root@localhost .ssh]# vim id_rsa.pub 
```  

4 . 配置用户名和邮箱

```language-bash
[root@localhost .ssh]# git config --global user.name 'bandwagonhost vps'
[root@localhost .ssh]# git config --global user.email 'this.xqs@gmail.com'
```

5. 测试连接

```language-bash
[root@localhost .ssh]# ssh -T git@github.com
Hi mmrxia! You've successfully authenticated, but GitHub does not provide shell access.
```


