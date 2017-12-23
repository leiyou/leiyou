title:  "Linux 命令学习笔记一"
date:   2015-06-04
tags: [Linux] 
---
###### 1、新建用户
<!-- 高亮显示背景，用于标识代码块 -->
``` bash
$ useradd [options] uername	
$ adduser username
```
注：这两个命令都可以创建新用户，adduser会自动完成新用户的基本设置，所以adduser较适合初学者使用;

###### useradd命令参数：
<img src="/images/study/useradd.png" alt="useradd command">

如：

``` bash 
$ useradd -d me	#则会新建一个me用户，并会在home目录下建立一个me目录，作为用户me的主目录
$ passwd me	
```

###### 2、显示当前用户

``` bash 
$ who am i
```
###### 3、删除/切换 用户

``` bash 
$ userdel -r username
$ su username
```
#### 网络类
###### 查看网络信息
``` bash 
$ ifconfig [options]
```

###### 设置ip
``` bash 
$ ifconfig eth0 youripaddress netmask yourmask
```

###### 关闭防火墙
``` bash 
$ iptables -F
```

<!-- 引用图片 -->
<!-- <img src="{{ site.baseurl }}/images/red.png" alt=""> -->
