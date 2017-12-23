title:  "Git 学习笔记一" 
date:   2015-06-15 
tags: [Git, Cvs]
---

前几天看了下《Pro Git》这本书，简单的了解关于Git的知识，在这里简单的记录一下书中的一些内容。

##1、配置用户信息

``` bash
$ git config -global user.name "username"
$ git config -global user.email "email"	
```
这两条命令可以设置用户名和电子邮件地址，每次git提交时都会引用这两条信息，记录谁提交了信息
相当于设置用户的身份信息，这些配置信息可在用户主目录的.gitconfig文件中看到

## 2、设置文本编辑器

``` bash
$ git config --global core.editor "your edit"
$ git config --global core.editor vim //比如设置vim为git的默认文本编辑器
```

## 3、查看配置信息
``` bash
$ git config --list //该命令会输出你设置的用户名、email、默认编辑器等等信息

$ git config user.name //这种方式可以直接查看某个环境变量的设置
```

## 4、获取帮助信息
``` bash
$ git --help
```

## 5、初始化新仓库
``` bash
$ git init

#该命令是在新项目中使用git管理时使用，初始化后会生成一个.git目录
#通过git clone  得到的目录中，一般已经包含.git目录		
```
