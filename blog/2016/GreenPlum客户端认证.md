---
title: GreenPlum客户端认证
date: 2016-08-01 14:47:00
tags: Greenplum
---

客户端连接master节点时，gp会验证连接的客户端ip，需要在**pg_hba.conf**配置文件中添加相应的记录才能连接到数据库。
添加记录的格式如下：
<pre>
hostssl    DATABASE  USER  CIDR-ADDRESS  METHOD  [OPTIONS]

hostnossl  DATABASE  USER  CIDR-ADDRESS  METHOD  [OPTIONS]
</pre>
说明：

  - 第一列指连接方式，客户端使用的是host方式
  - 第二列指要连接的数据库名，可以是指定数据库或是所有数据库(all)
  - 第三列指连接该数据库的用户
  - 第四列，**local方式**指的是验证方式，其他三种方式指的是ip地址段
  - 第五列，**local方式**是可选参数，其他三列指认证方式

 > method可选的值有：

 1. reject （拒绝连接）
 2. trust （不需要验证）
 3. md5
 4. password （未加密密码）
 5. ident （local 用户连接）

如：
<pre>
local    all         gpadmin         ident
host     all         gpadmin         127.0.0.1/28    trust
host     all         gpadmin         172.17.0.220/32       trust
host     all         gpadmin         172.17.0.221/32       trust
</pre>

添加记录后，以gpadmin用户执行**gpstop -u**重新加载配置配置.


*注：添加记录只需在master节点的pg_hba.conf文件中修改即可*