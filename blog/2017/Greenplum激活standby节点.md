---
title: Greenplum激活standby节点
date: 2017-01-11 08:03:00
tags: Greenplum
---

激活前需确定安装GP时已配置了standby节点，standby作为master的备份节点，当GP集群中master出故障挂了后，可以激活standby节点，激活后的standby成为集群中新的master，可继续管理集群的运行。

测试所有的GP环境有1台master，1台standby，2台segment节点, 主机名：
``` shell
mdw
smdw
sdw1
sdw2
```

1、查看GP运行时的状态

``` shell
[gpadmin@mdw ~]$ gpstate -a
```

``` shell
gpstate:mdw:gpadmin-[INFO]:-Greenplum instance status summary
gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:mdw:gpadmin-[INFO]:-   Master instance                                = Active
gpstate:mdw:gpadmin-[INFO]:-   Master standby                                 = smdw
gpstate:mdw:gpadmin-[INFO]:-   Standby master state                           = Standby host passive
gpstate:mdw:gpadmin-[INFO]:-   Total segment instance count from metadata     = 18
gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:mdw:gpadmin-[INFO]:-   Primary Segment Status
gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:mdw:gpadmin-[INFO]:-   Total primary segments                         = 18
gpstate:mdw:gpadmin-[INFO]:-   Total primary segment valid (at master)        = 18
gpstate:mdw:gpadmin-[INFO]:-   Total primary segment failures (at master)     = 0
gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid files missing   = 0
gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid files found     = 18
gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid PIDs missing    = 0
gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid PIDs found      = 18
gpstate:mdw:gpadmin-[INFO]:-   Total number of /tmp lock files missing        = 0
gpstate:mdw:gpadmin-[INFO]:-   Total number of /tmp lock files found          = 18
gpstate:mdw:gpadmin-[INFO]:-   Total number postmaster processes missing      = 0
gpstate:mdw:gpadmin-[INFO]:-   Total number postmaster processes found        = 18
gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:mdw:gpadmin-[INFO]:-   Mirror Segment Status
gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:mdw:gpadmin-[INFO]:-   Mirrors not configured on this array
gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
```
* 输出的信息可看到standby节点已配置且状态是paasive的。*

2、standby节点是在master节点出故障的情况下才激活的，因此激活standby节点前先关闭master节点。

``` shell
[gpadmin@mdw ~]$ gpstop -m
```

3、进入standby节点主机smdw，激活standby
``` shell
[gpadmin@smdw ~]$ gpactivatestandby -d ${MASTER_DATA_DIRECTORY}
```

``` shell
gpactivatestandby:smdw:gpadmin-[INFO]:------------------------------------------------------
gpactivatestandby:smdw:gpadmin-[INFO]:-Standby data directory    = /data/gpadmin/master/gpseg-1
gpactivatestandby:smdw:gpadmin-[INFO]:-Standby port              = 5432
gpactivatestandby:smdw:gpadmin-[INFO]:-Standby running           = yes
gpactivatestandby:smdw:gpadmin-[INFO]:-Force standby activation  = no
gpactivatestandby:smdw:gpadmin-[INFO]:------------------------------------------------------
Do you want to continue with standby master activation? Yy|Nn (default=N):
> y
gpactivatestandby:smdw:gpadmin-[INFO]:-found standby postmaster process
gpactivatestandby:smdw:gpadmin-[INFO]:-Updating transaction files filespace flat files...
gpactivatestandby:smdw:gpadmin-[INFO]:-Updating temporary files filespace flat files...
gpactivatestandby:smdw:gpadmin-[INFO]:-Promoting standby...
gpactivatestandby:smdw:gpadmin-[DEBUG]:-Waiting for connection...
gpactivatestandby:smdw:gpadmin-[INFO]:-Standby master is promoted
gpactivatestandby:smdw:gpadmin-[INFO]:-Reading current configuration...
gpactivatestandby:smdw:gpadmin-[DEBUG]:-Connecting to dbname='gpsdb'
gpactivatestandby:smdw:gpadmin-[INFO]:-Writing the gp_dbid file - /data/gpadmin/master/gpseg-1/gp_dbid...

gpactivatestandby:smdw:gpadmin-[INFO]:-But found an already existing file.
gpactivatestandby:smdw:gpadmin-[INFO]:-Hence removed that existing file.
gpactivatestandby:smdw:gpadmin-[INFO]:-Creating a new file...
gpactivatestandby:smdw:gpadmin-[INFO]:-Wrote dbid: 1 to the file.
gpactivatestandby:smdw:gpadmin-[INFO]:-Now marking it as read only...
gpactivatestandby:smdw:gpadmin-[INFO]:-Verifying the file...
gpactivatestandby:smdw:gpadmin-[INFO]:------------------------------------------------------
gpactivatestandby:smdw:gpadmin-[INFO]:-The activation of the standby master has completed successfully.
```

4、standby激活后成为新的master，此时查看GP的状态可看到standby节点未配置状态
``` shell
[gpadmin@smdw ~]$ gpstate -a
```

``` shell
gpstate:smdw:gpadmin-[INFO]:-Greenplum instance status summary
gpstate:smdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:smdw:gpadmin-[INFO]:-   Master instance                                = Active
gpstate:smdw:gpadmin-[INFO]:-   Master standby                                 = No master standby configured
gpstate:smdw:gpadmin-[INFO]:-   Total segment instance count from metadata     = 18
gpstate:smdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:smdw:gpadmin-[INFO]:-   Primary Segment Status
gpstate:smdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:smdw:gpadmin-[INFO]:-   Total primary segments                         = 18
gpstate:smdw:gpadmin-[INFO]:-   Total primary segment valid (at master)        = 18
gpstate:smdw:gpadmin-[INFO]:-   Total primary segment failures (at master)     = 0
gpstate:smdw:gpadmin-[INFO]:-   Total number of postmaster.pid files missing   = 0
gpstate:smdw:gpadmin-[INFO]:-   Total number of postmaster.pid files found     = 18
gpstate:smdw:gpadmin-[INFO]:-   Total number of postmaster.pid PIDs missing    = 0
gpstate:smdw:gpadmin-[INFO]:-   Total number of postmaster.pid PIDs found      = 18
gpstate:smdw:gpadmin-[INFO]:-   Total number of /tmp lock files missing        = 0
gpstate:smdw:gpadmin-[INFO]:-   Total number of /tmp lock files found          = 18
gpstate:smdw:gpadmin-[INFO]:-   Total number postmaster processes missing      = 0
gpstate:smdw:gpadmin-[INFO]:-   Total number postmaster processes found        = 18
gpstate:smdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:smdw:gpadmin-[INFO]:-   Mirror Segment Status
gpstate:smdw:gpadmin-[INFO]:-----------------------------------------------------
gpstate:smdw:gpadmin-[INFO]:-   Mirrors not configured on this array
gpstate:smdw:gpadmin-[INFO]:-----------------------------------------------------
```

5、此时standby节点已激活，若想要恢复原来的master节点也很简单，只需先将原有的master主机设为standby节点，之后再执行一遍上面的步骤即可.

``` shell
[gpadmin@smdw ~]$ gpinitstandby -s master  #新建standby
```

报错：
``` shell
smdw:gpadmin-[INFO]:-Validating environment and parameters for standby initialization...
smdw:gpadmin-[INFO]:-Checking for filespace directory /data/gpadmin/master/gpseg-1 on master
smdw:gpadmin-[ERROR]:-Filespace directory already exists on host master
smdw:gpadmin-[ERROR]:-Failed to create standby
smdw:gpadmin-[ERROR]:-Error initializing standby master: master data directory exists
```

移除掉原来master上的 /data/gpadmin/master/gpseg-1目录即可.

``` shell
smdw:gpadmin-[INFO]:------------------------------------------------------
smdw:gpadmin-[INFO]:-Greenplum master hostname               = smdw
smdw:gpadmin-[INFO]:-Greenplum master data directory         = /data/gpadmin/master/gpseg-1
smdw:gpadmin-[INFO]:-Greenplum master port                   = 5432
smdw:gpadmin-[INFO]:-Greenplum standby master hostname       = master
smdw:gpadmin-[INFO]:-Greenplum standby master port           = 5432
smdw:gpadmin-[INFO]:-Greenplum standby master data directory = /data/gpadmin/master/gpseg-1
smdw:gpadmin-[INFO]:-Greenplum update system catalog         = On
smdw:gpadmin-[INFO]:------------------------------------------------------
smdw:gpadmin-[INFO]:- Filespace locations
smdw:gpadmin-[INFO]:------------------------------------------------------
smdw:gpadmin-[INFO]:-pg_system -> /data/gpadmin/master/gpseg-1
Do you want to continue with standby master initialization? Yy|Nn (default=N):
> y
smdw:gpadmin-[INFO]:-Syncing Greenplum Database extensions to standby
smdw:gpadmin-[INFO]:-The packages on master are consistent.
smdw:gpadmin-[INFO]:-Adding standby master to catalog...
smdw:gpadmin-[INFO]:-Database catalog updated successfully.
smdw:gpadmin-[INFO]:-Updating pg_hba.conf file...
smdw:gpadmin-[INFO]:-pg_hba.conf files updated successfully.
smdw:gpadmin-[INFO]:-Updating filespace flat files...
smdw:gpadmin-[INFO]:-Filespace flat file updated successfully.
smdw:gpadmin-[INFO]:-Starting standby master
smdw:gpadmin-[INFO]:-Checking if standby master is running on host: master  in directory: /data/gpadmin/master/gpseg-1
smdw:gpadmin-[INFO]:-Cleaning up pg_hba.conf backup files...
smdw:gpadmin-[INFO]:-Backup files of pg_hba.conf cleaned up successfully.
smdw:gpadmin-[INFO]:-Successfully created standby master on master
```

6、关闭master节点
``` shell
[gpadmin@smdw ~]$ gpstop -m 
```

7、 切换到master主机，激活standby
``` shell
[gpadmin@mdw ~]$ gpactivatestandby -d ${MASTER_DATA_DIRECTORY}
```
激活成功后，master节点又恢复为原来的主机，为了防止master主机再次挂掉，还是要新建一个standby节点做备用.


standby激活时可能会遇到的问题：
---

* 提示PGPORT未指定
这可能是安装GP时.bash_profile 文件里未配置端口.

``` shell
[gpadmin@smdw ~]$ cat .bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

source /usr/local/greenplum-db/greenplum_path.sh
export MASTER_DATA_DIRECTORY=/data/gpadmin/master/gpseg-1
export PGPORT=5432  # <<<< 这里
export PGDATABASE=gpsdb

export PATH
```
** 查看gpinitstandby 的相关命令：**

``` shell
[gpadmin@smdw ~]$ gpactivatestandby

A backup, standby master host serves as a 'warm standby' in the 
event of the primary master host becoming non-operational. The standby 
master is kept up to date by transaction log replication processes (the 
walsender and walreceiver), which run on the primary master and standby 
master hosts and keep the data between the primary and standby master 
hosts synchronized. If the primary master fails, the log replication 
process is shut down, and the standby master can be activated in its 
place by using the gpactivatestandby utility. Upon activation of the 
standby master, the replicated logs are used to reconstruct the state of 
the master host at the time of the last successfully committed 
transaction. 

The activated standby master effectively becomes the Greenplum Database 
master, accepting client connections on the master port and performing 
normal master operations such as SQL command processing and workload 
management. 


*****************************************************
OPTIONS
*****************************************************

-a (do not prompt) 

 Do not prompt the user for confirmation. 


-D (debug) 

 Sets logging level to debug. 


-F <list_of_filespaces> 

 A list of filespace names and the associated locations. Each filespace 
 name and its location is separated by a colon. If there is more than one 
 file space name, each pair (name and location) is separated by a comma. 
 For example: 

 filespace1_name:fs1_location,filespace2_name:fs2_location 

 If this option is not specified, gpinitstandby prompts the user for the 
 filespace names and locations. 

 If the list is not formatted correctly or number of filespaces do not 
 match the number of filespaces already created in the system, 
 gpinitstandby returns an error. 


-l <logfile_directory> 

 The directory to write the log file. Defaults to ~/gpAdminLogs. 


-n (restart standby master) 

 Specify this option to start a Greenplum Database standby master that 
 has been configured but has stopped for some reason. 


-P <port> 

 This option specifies the port that is used by the Greenplum Database 
 standby master. The default is the same port used by the active 
 Greenplum Database master. 

 If the Greenplum Database standby master is on the same host as the 
 active master, the ports must be different. If the ports are the same 
 for the active and standby master and the host is the same, the utility 
 returns an error. 


-q (no screen output) 

 Run in quiet mode. Command output is not displayed on the screen, but is 
 still written to the log file. 


-r (remove standby master) 

 Removes the currently configured standby master host from your Greenplum 
 Database system. 


-s <standby_hostname> 

 The host name of the standby master host. 


-v (show utility version) 

 Displays the version, status, last updated date, and check sum of this 
 utility. 


-? (help) 

 Displays the online help. 


*****************************************************
EXAMPLES
*****************************************************

Add a standby master host to your Greenplum Database system and start 
the synchronization process: 

gpinitstandby -s host09 

Start an existing standby master host and synchronize the data with the 
current primary master host: 

gpinitstandby -n 

NOTE: Do not specify the -n and -s options in the same command.
```



激活standby节点的相关资料看参考pivotal gpdb官网:
---

[Recovering a Failed Master](http://gpdb.docs.pivotal.io/4370/admin_guide/highavail/topics/g-recovering-a-failed-master.html)

[Enabling High Availability Features](http://gpdb.docs.pivotal.io/4370/admin_guide/highavail/topics/g-enabling-high-availability-features.html)

** 本文中参考的文章有：**

[greenplum如何激活，同步,删除standby和恢复原始master
](http://www.cnblogs.com/lottu/p/5683988.html)