---
title: mysql忘记root用户密码
date: 2017-01-11 22:40:54
tags:
---

Linux下安装mysql时设置了root用户密码，过了段时间想登录数据库时发现密码忘记了，试了好多遍都不对，无奈只能强制更新密码。搜索了一下网上有不少重置mysql root密码的文章，这里也对更新过程做个记录.

1、停止mysql服务

``` shell
# service mysqld stop
```

2、安全模式下启动mysql并让其进程后台运行

``` shell

# /usr/bin/mysqld_safe --skip-grant-tables &
```
*--skip-grant-tables 参数是指启动mysql服务时跳过权限表的认证，启动后连接mysql不需要密码即可登录*

3、root用户登录数据库

``` shell
# mysql -u root
```

4、 更新密码
``` shell
mysql> use mysql;
mysql> update user set password=PASSWORD("newpasswd") where User='root';
mysql> flush privileges;
mysql> exit
```

5、关闭安全模式，重启mysql服务

``` shell
# ps -ef | grep mysql
root      2465     1  0 04:14 pts/1    00:00:00 /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --socket=/var/lib/mysql/mysql.sock --pid-file=/var/run/mysqld/mysqld.pid --basedir=/usr --user=mysql
mysql     2585  2465  0 04:14 pts/1    00:00:02 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --user=mysql --log-error=/var/log/mysqld.log --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/lib/mysql/mysql.sock
```

``` shell
# kill 2585 # kill mysql进程
```

6、重启mysql服务

``` shell
# service mysqld start
```

7、登录mysql数据库

``` shell
# mysql -u root -p # 输入新密码登录
```
