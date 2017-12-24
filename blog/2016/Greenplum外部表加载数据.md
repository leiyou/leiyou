---
title: Greenplum外部表加载数据
date: 2016-11-5 13:21:00
tags: Greenplum
---


nohup gpfdist -d filepath -p 8081 &
```
上面的命令可以使gpfdist 进程在后台运行, *-d* 是存放csv, txt 文件数据的目录, csv, txt数据必须是以相同分隔符分隔的数据，如以 **,** 或是 **|**, *-p*指定的是服务的端口.

- 查看gpfdist服务是否启动：
``` shell
ps -ax | grep gpfdist
```

- gpfdist成功启动后，登录数据库创建外部表

创建的外部表的列需要与csv数据中分隔符中的列一致，如：

<pre>
csv: 
粤A00001, 112.12, 22.50, 65, 7, 1, 2015-10-01
粤A00001, 112.12, 22.50, 65, 7, 1, 2015-10-01
粤A00001, 112.12, 22.50, 65, 7, 1, 2015-10-01
</pre>

创建外部表：
<pre>
create external table employ (
  taxi_no character varying(16),
  longitude numeric(10,6),
  latitude numeric(10,6),
  speed numeric(3,0),
  direction numeric(1,0),
  status numeric(1,0),
  record_date
) 
location('gpfdist:http://ip:8081/*.csv')
format 'csv' (delimiter ',' null '')
encoding utf8
log errors into error
segment reject limit 1000;
</pre>
通过这种*IP:PORT*的方式，可以将存放在不同节点服务器上的数据加载到外部表中；

这里需要设置的参数比较多，有时候可能会忘记具体的参数该如何设置，使用gp提供的 **\h sql 命令**命令可以查看使用方法，如

``` shell
$ \h create external table
```

![help](/content/images/2016/11/1.jpg)

通过外部表的方式可以很快的将数据加载到表中，对数据进行迁移时，这种方式比较高效。近期的一个项目中需要将oracle中的大数据量迁移到gp里，刚开始的时候是使用**kettle**来实现的，但速度比较慢。后来采用外部表的方式，将oracle中的数据先导出为csv格式数据，再导入到gp中。外部表充当一个周转表的角色，CRUD的操作不应在外部表中执行，另外当gpfdist服务停止后，外部表中的数据也会消失。