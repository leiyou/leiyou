---
title: git 多账户使用
date: 2016-10-16 14:46:00
tags: Git
---

使用了两个github账户, 一个是个人用的*(A账户)*, 另外一个则是用于特定项目使用*(B账户)*. 平时使用的基本都是A, 提交时也没啥问题. 但今天添加了B的远程仓库，git push时却出了错.

情况是这样：

1. 登录到B账户，新建Apps仓库

2. git bash命令行添加远程仓库, 并push到远程仓库

``` shell
$ mkdir Apps
$ git init
$ echo 'temp note' > readme.md
$ git add . -A
$ git commit -m 'first commit'
$ git remote add origin git@github.com:SZJWGPS/Apps.git
$ git push -u origin master
```

结果是这样：

<pre>
ERROR: Permission to SZJWGPS/Apps.git denied to yourlei.
fatal: Could not read from remote repository.
Please make sure you have the correct access rights
and the repository exists.
</pre>

百度折腾了会，查看了下 *C:\Users\admin\.ssh.ssh*, 有这么几个文件
![img](/content/images/2016/10/ssh.jpg)

> id_rsa是账户A生成的*ssh-key*, 而my则是账户B的*ssh-key*

*config*文件中的内容长这样：

<pre>
Host github.com  
    HostName github.com  
    PreferredAuthentications publickey  
    IdentityFile ~/.ssh/id_rsa  
  
Host my.github.com  
    HostName github.com  
    PreferredAuthentications publickey  
    IdentityFile ~/.ssh/my 
</pre>

生成账户A的ssh-key 时是直接敲了三次**enter**键, 所以默认生成的key的文件名是id_rsa, 而在生成账户B的ssh-key时则是有意输入*my*作为ssh-key的文件名。config中的两个**Host**的配置，可以看做是两个github账户的相应配置，当添加远程仓库是如果写成这样：

``` shell
$ git remote add origin git@github.com:SZJWGPS/Apps.git
```
实际上是使用了主机*github.com*即账户A的配置去进行验证，显然*IdentityFile*应该是账户A的key, 而不是SZJWGPS(账户B), 因此会出现前面的错误.

> git@github.com:SZJWGPS/Apps.git可理解为
git@Host:username/yourrepository, git@之后的Host实际会被HostName属性替代.

知道了这点，所以很容易就可以修正错误.

``` shell
$ git remote rm origin
$ git remote add origin git@my.github.com:SZJWGPS/Apps.git
```
这样再次push到远程仓库时, 就会使用*my.github.com*主机的属性值与*SZJWGPS*账户B验证, 也就顺利push到远程仓库.
