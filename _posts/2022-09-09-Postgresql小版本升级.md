# 			          Postgresql小版本升级

# 一：目的

根据商务局一年一度安全等保，第三方对单一窗口平台漏洞扫描评测，根据漏洞扫描报告，发现数据库存在多种漏洞

当前设备中使用的PostgreSQL数据版本是10.6。而PostgreSQL官方最新的版本是14.5.已经迭代了4个大版本，中间每个大版本又有若干个次版本的迭代更新，在每次的迭代过程中，都有许多的隐藏缺陷修复以及性能的提升，包括CPU、内存占用、磁盘读写、网络I/O、索引逻辑等等，根据官方漏洞修复说明，先将旧版本PostgreSQL10.6数据库更替为官方较稳定的PostgreSQL10.22版本。

PostgreSQL10.22版本发布2022-08-11，目前处于维护周期

## 1.1：风险评估

PostgreSQL数据库版本更新之间内部关系发生了变化，因此主要的风险来自以下两个：

* 在数据库升级之前，数据能否完整备份，以及数据库成功升级后，是否有丢失现象
* 备份的旧数据能否理想的作用于新版本的数据库中

## 1.2：漏洞信息

漏洞扫描：

PostgreSQL adminpack扩展安全漏洞(CVE-2018-11150)

PostgreSQL SQL注入漏洞(CVE-2020-25695)

PostgreSQL 安全漏洞(CVE-2018-10589)

PostgreSQL 代码问题漏洞(CVE-2020-14350)

PostgreSQL 不安全临时文件创建漏洞(CVE-2018-1053

## 1.3：升级方法

**利用 pg_upgrade 工具进行升级**

pg_upgrade 工具可以支持 PostgreSQL 跨版本的就地升级，不需要执行导出和导入操作。pg_upgrade 可以支持 PostgreSQL 8.4.X 到最新版本的升级，包括快照版本和测试版本。

pg_upgrade 提供了升级前的兼容性检查（-c 或者 --check 选项）功能， 可以发现插件、数据类型不兼容等问题。如果指定了 --link 选项，新版本服务可以直接使用原有的数据库文件而不需要执行复制，通常可以在几分钟内完成升级操作。



下面我们介绍这种升级方法的具体操作，假如当前 PostgreSQL 软件的安装目录位于 /home/postgres/pgsql10.6，同时数据目录位于 /home/postgres/pgdata，我们将其升级为 PostgreSQL 10.22。

## 1.4：数据库备份

* 服务器快照
* 数据目录进行备份



## 1.5：操作过程

* 停止数据库
* Postgresql10.22部署
* 备份原文件数据目录
* 使用工具升级
* 启动数据库验证



# 二：PostgreSQL10.6搭建（本机搭建可忽略）

## 2.1: 环境准备

以下操作是在本机搭建测试，目的是升级线上数据库环境，为了本机环境更接近线上环境，数据库配置文件、用户、操作系统，目录位置等等，都采用线上环境，

由于线上PgSQL版本是10.6，本机搭建10.6版本，保持线上一致性

首先进行环境准备，创建用户和组，下载依赖环境，创建pgdata(数据目录)，soft(包目录)，pgsql10.6(程序目录)，上传PgSQL10.6、PgSQL10.22包到soft目录下面

```bash
#用户创建
[root@s1 ~]# groupadd postgres -g 1000
[root@s1 ~]# useradd postgres -u 1000 -g 1000 -m -s /bin/bash
[root@s1 ~]# echo "postgres" | passwd --stdin postgres        
Changing password for user postgres.
passwd: all authentication tokens updated successfully.

#下载依赖
[root@s1 ~]# yum -y install gcc gcc-c++ make openssl openssl-devel openssh-server openssh-clients readline-devel
[root@s1 ~]# su - postgres
[postgres@s1 ~]$ mkdir {pgdata,soft,pgsql10.6}

[postgres@s1 ~]$ cd soft/
[postgres@s1 soft]$ ll
total 51140
-rw-r--r--. 1 postgres postgres 25460734 Aug 31 17:24 postgresql-10.22.tar.gz
-rw-r--r--. 1 postgres postgres 26902911 Aug 31 17:23 postgresql-10.6.tar.gz
```

## 2.2：PgSQL10.6部署



数据库部署步骤

1.解压-编译

2.全局环境变量配置（由于线上环境变量配置在全局，本机配置在全局）

3.数据库初始化

4.线上postgresql.conf pg_hba.conf配置文件拷贝到/home/postgres/pgdata/目录下面

5.启动服务

```bash
[postgres@s1 soft]$tar xf postgresql-10.6.tar.gz
[postgres@s1 soft]$cd postgresql-10.6
[postgres@s1 postgresql-10.6]$ ./configure --prefix=/home/postgres/pgsql10.6
[postgres@s1 postgresql-10.6]$ make
[postgres@s1 postgresql-10.6]$ make install
#root
[root@s1 ~]# vim /etc/profile
export PATH=/home/postgres/pgsql10.6/bin:$PATH
export LD_LIBRARY_PATH=/home/postgres/pgsql10.6/lib:$LD_LIBRARY_PATH
export PGDATA=/home/postgres/pgdata
[root@s1 ~]# source /etc/profile

#数据库初始化
[postgres@s1 ~]$ /home/postgres/pgsql10.6/bin/initdb -D /home/postgres/pgdata -U postgres

#配置于生产环境保持一致
[postgres@s1 pgdata]$ vi postgresql.conf
listen_addresses = '*'
port = 5432 
max_connections = 800
shared_buffers = 500MB
dynamic_shared_memory_type = posix
log_destination = 'csvlog'
logging_collector = on
log_min_duration_statement = 60
log_checkpoints = on
log_statement = 'mod'
log_timezone = 'PRC'
log_lock_waits = on
datestyle = 'iso, mdy'
timezone = 'PRC'
lc_messages = 'en_US.UTF-8'
lc_monetary = 'en_US.UTF-8'
lc_numeric = 'en_US.UTF-8
lc_time = 'en_US.UTF-8'
default_text_search_config = 'pg_catalog.english
[postgres@s1 pgdata]$ vi pg_hba.conf
host	all		all		0.0.0.0/0	md5
#启动
[postgres@s1 pgdata]$ pg_ctl start 
```

## 2.3：插入数据

进入数据库创建数据库和表插入数据，用于升级之后进行数据验证

```bash
#插入测试数据
[postgres@s1 ~]$ psql -U postgres
psql (10.6)
Type "help" for help.

postgres=# create user test with password 'test';
postgres=# create database test owner test;
postgres=# \c test
test=# create table test(id int);
test=# insert into test values(111);
test=# insert into test values(222);
test=# select *from test;
 id  
-----
 111
 222
(2 rows
```

## 2.4: 开机启动脚本

开机启动脚本使用了线上环境脚本

```bash
[postgres@s1 start-scripts]# pwd
/home/postgres/soft/postgresql-10.6/contrib/start-scripts
[root@s1 start-scripts]# cd /home/postgres/soft/postgresql-10.6/contrib/start-scripts
[root@s1 start-scripts]# cp linux /etc/init.d/postgres
[root@s1 start-scripts]# chmod +x /etc/init.d/postgres



[root@s1 start-scripts]# cat /etc/init.d/postgres 
#! /bin/sh

# chkconfig: 2345 98 02
# description: PostgreSQL RDBMS

# This is an example of a start/stop script for SysV-style init, such
# as is used on Linux systems.  You should edit some of the variables
# and maybe the 'echo' commands.
#
# Place this file at /etc/init.d/postgresql (or
# /etc/rc.d/init.d/postgresql) and make symlinks to
#   /etc/rc.d/rc0.d/K02postgresql
#   /etc/rc.d/rc1.d/K02postgresql
#   /etc/rc.d/rc2.d/K02postgresql
#   /etc/rc.d/rc3.d/S98postgresql
#   /etc/rc.d/rc4.d/S98postgresql
#   /etc/rc.d/rc5.d/S98postgresql
# Or, if you have chkconfig, simply:
# chkconfig --add postgresql
#
# Proper init scripts on Linux systems normally require setting lock
# and pid files under /var/run as well as reacting to network
# settings, so you should treat this with care.

# Original author:  Ryan Kirkpatrick <pgsql@rkirkpat.net>

# contrib/start-scripts/linux

## EDIT FROM HERE

# Installation prefix
prefix=/home/postgres/pgsql10.6

# Data directory
PGDATA="/home/postgres/pgdata"

# Who to run the postmaster as, usually "postgres".  (NOT "root")
PGUSER=postgres

# Where to keep a log file
PGLOG="$PGDATA/serverlog"

# It's often a good idea to protect the postmaster from being killed by the
# OOM killer (which will tend to preferentially kill the postmaster because
# of the way it accounts for shared memory).  To do that, uncomment these
# three lines:
#PG_OOM_ADJUST_FILE=/proc/self/oom_score_adj
#PG_MASTER_OOM_SCORE_ADJ=-1000
#PG_CHILD_OOM_SCORE_ADJ=0
# Older Linux kernels may not have /proc/self/oom_score_adj, but instead
# /proc/self/oom_adj, which works similarly except for having a different
# range of scores.  For such a system, uncomment these three lines instead:
#PG_OOM_ADJUST_FILE=/proc/self/oom_adj
#PG_MASTER_OOM_SCORE_ADJ=-17
#PG_CHILD_OOM_SCORE_ADJ=0

## STOP EDITING HERE

# The path that is to be used for the script
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# What to use to start up the postmaster.  (If you want the script to wait
# until the server has started, you could use "pg_ctl start" here.)
DAEMON="$prefix/bin/postmaster"

# What to use to shut down the postmaster
PGCTL="$prefix/bin/pg_ctl"

set -e

# Only start if we can find the postmaster.
test -x $DAEMON ||
{
        echo "$DAEMON not found"
        if [ "$1" = "stop" ]
        then exit 0
        else exit 5
        fi
}

# If we want to tell child processes to adjust their OOM scores, set up the
# necessary environment variables.  Can't just export them through the "su".
if [ -e "$PG_OOM_ADJUST_FILE" -a -n "$PG_CHILD_OOM_SCORE_ADJ" ]
then
        DAEMON_ENV="PG_OOM_ADJUST_FILE=$PG_OOM_ADJUST_FILE PG_OOM_ADJUST_VALUE=$PG_CHILD_OOM_SCORE_ADJ"
fi


# Parse command line parameters.
case $1 in
  start)
        echo -n "Starting PostgreSQL: "
        test -e "$PG_OOM_ADJUST_FILE" && echo "$PG_MASTER_OOM_SCORE_ADJ" > "$PG_OOM_ADJUST_FILE"
        su - $PGUSER -c "$DAEMON_ENV $DAEMON -D '$PGDATA' >>$PGLOG 2>&1 &"
        echo "ok"
        ;;
  stop)
        echo -n "Stopping PostgreSQL: "
        su - $PGUSER -c "$PGCTL stop -D '$PGDATA' -s"
        echo "ok"
        ;;
  restart)
        echo -n "Restarting PostgreSQL: "
        su - $PGUSER -c "$PGCTL stop -D '$PGDATA' -s"
        test -e "$PG_OOM_ADJUST_FILE" && echo "$PG_MASTER_OOM_SCORE_ADJ" > "$PG_OOM_ADJUST_FILE"
        su - $PGUSER -c "$DAEMON_ENV $DAEMON -D '$PGDATA' >>$PGLOG 2>&1 &"
        echo "ok"
        ;;
  reload)
        echo -n "Reload PostgreSQL: "
        su - $PGUSER -c "$PGCTL reload -D '$PGDATA' -s"
        echo "ok"
        ;;
  status)
        su - $PGUSER -c "$PGCTL status -D '$PGDATA'"
        ;;
  *)
        # Print help
        echo "Usage: $0 {start|stop|restart|reload|status}" 1>&2
        exit 1
        ;;
esac

exit 0
```





## 2.5：注意事项

以上信息均为部署PostgreSQL10.6步骤，目的于线上环境保持一致，可忽略，线上环境不需要操作

以下信息操作需要在线上操作



# 三：PostgreSQL升级



## 2.1：升级工具

在程序的bin目录下，提供了很多的数据库工具，有一个pg_upgrade的工具就是专门用于数据库升级的。关于该工具可以使用帮助命令来查看具体的用法：

```bash
postgres@s1 ~]$ pg_upgrade --help
pg_upgrade 将 PostgreSQL 集群升级到不同的主要版本。

用法：
  pg_upgrade [选项] ...

选项：
  -b, --old-bindir=BINDIR 旧集群可执行目录
  -B, --new-bindir=BINDIR 新集群可执行目录
  -c, --check 只检查集群，不改变任何数据
  -d, --old-datadir=DATADIR 旧集群数据目录
  -D, --new-datadir=DATADIR 新集群数据目录
  -j, --jobs 同时使用的进程或线程数
  -k, --link 链接而不是将文件复制到新集群
  -o, --old-options=OPTIONS 传递给服务器的旧集群选项
  -O, --new-options=OPTIONS 传递给服务器的新集群选项
  -p, --old-port=PORT 旧集群端口号（默认 50432）
  -P, --new-port=PORT 新集群端口号（默认50432）
  -r, --retain 成功后保留SQL和日志文件
  -U, --username=NAME 集群超级用户（默认“postgres”）
  -v, --verbose 启用详细内部日志记录
  -V, --version 显示版本信息，然后退出
  -?, --help 显示此帮助，然后退出
```



帮助文件中，提到了使用pg_upgrade工具前，必须创建一个新的数据库，并且是已经初始化的，同时关闭原来的数据库和新的数据库。使用pg_upgrade时候，必须要加上前后版本的data目录和bin目录。



## 2.2: 升级过程

首先确认的是，原来的数据库版本是pg10.6，数据目录在/home/postgres/pgdata。然后，安装完pg10.22后，不要初始化目录。将原来的9.6版本数据目录重命名为pgdata.old

先停止数据库，对原来的数据目录重命名

在/home/postgres/下面创建一个pgdata目录，作为新版本的数据库数据目录，需要注意的是，这个目录权限是700，owner是postgres

```bash
[postgres@s1 ~]$ pwd
/home/postgres
[postgres@s1 ~]$ pg_ctl stop
[postgres@s1 ~]$ mv pgdata pgdata.old
[postgres@s1 ~]$mkdir pgdata
```



### 2.2.1：PostgreSQL10.22部署

postgreSQL10.22版本传到数据包目录下面，进行编译-安装

环境变量控制在当前用户下面

数据库初始化

版本验证

```bash
[postgres@s1 soft]$ pwd
/home/postgres/soft
[postgres@s1 soft]$ ll postgresql-10.22.tar.gz 
-rw-r--r--. 1 postgres postgres 25460734 Aug 31 17:24 postgresql-10.22.tar.gz

[postgres@s1 soft]$ tar -xf postgresql-10.22.tar.gz 
[postgres@s1 soft]$ cd postgresql-10.22
[postgres@s1 postgresql-10.22]$ ./configure --prefix=/home/postgres/pgsql10.22
[postgres@s1 postgresql-10.22]$make
[postgres@s1 postgresql-10.22]$make install
#注销掉全局变量
[root@s1 start-scripts]# vim /etc/profile
#export PATH=/home/postgres/pgsql10.6/bin:$PATH
#export LD_LIBRARY_PATH=/home/postgres/pgsql10.6/lib:$LD_LIBRARY_PATH
#export PGDATA=/home/postgres/pgdata
[root@s1 start-scripts]# source /etc/profile

#环境变量控制在用户下面
[postgres@s1 ~]$ cat ~/.bash_profile 
export PATH
export PATH=/home/postgres/pgsql10.22/bin:$PATH
export LD_LIBRARY_PATH=/home/postgres/pgsql10.22/lib:$LD_LIBRARY_PATH
export PGDATA=/home/postgres/pgdata

[postgres@s1 ~]$ source ~/.bash_profile
#初始化数据库
[postgres@s1 ~]$ /home/postgres/pgsql10.22/bin/initdb -D /home/postgres/pgdata -U postgres

#版本验证
[postgres@s1 ~]$ pg_ctl -V            
pg_ctl (PostgreSQL) 10.22
```



### 2.2.2：检测升级

进行升级check，注意后面加上-c，这一步只是检查不会实际执行升级。所有项都是ok即认为是可以升级。

```bash
[postgres@s1 ~]$ pg_upgrade -b /home/postgres/pgsql10.6/bin -B /home/postgres/pgsql10.22/bin -d /home/postgres/pgdata.old/ -D /home/postgres/pgdata/ -p 5432 -P 5432 -c
Performing Consistency Checks
-----------------------------
Checking cluster versions                                   ok
Checking database user is the install user                  ok
Checking database connection settings                       ok
Checking for prepared transactions                          ok
Checking for system-defined composite types in user tables  ok
Checking for reg* data types in user tables                 ok
Checking for contrib/isn with bigint-passing mismatch       ok
Checking for presence of required libraries                 ok
Checking database user is the install user                  ok
Checking for prepared transactions                          ok
Checking for new cluster tablespace directories             ok

*Clusters are compatible*



```



执行升级。即在上一步去掉-c，需要注意的是这一步根据数据库的大小执行时间长短不一，执行完毕后会产生两个脚本analyze_new_cluster.sh和delete_old_cluster.sh，根据实际需要来进行执行，一般都会执行第一个脚本，第二个不建议执行，以防需要回滚升级，保留原来的数据目录比较保险。

```bash
[postgres@s1 ~]$ pg_upgrade -b /home/postgres/pgsql10.6/bin -B /home/postgres/pgsql10.22/bin -d /home/postgres/pgdata.old/ -D /home/postgres/pgdata/ -p 5432 -P 5432
Performing Consistency Checks
-----------------------------
Checking cluster versions                                   ok
Checking database user is the install user                  ok
Checking database connection settings                       ok
Checking for prepared transactions                          ok
Checking for system-defined composite types in user tables  ok
Checking for reg* data types in user tables                 ok
Checking for contrib/isn with bigint-passing mismatch       ok
Creating dump of global objects                             ok
Creating dump of database schemas
                                                            ok
Checking for presence of required libraries                 ok
Checking database user is the install user                  ok
Checking for prepared transactions                          ok
Checking for new cluster tablespace directories             ok

If pg_upgrade fails after this point, you must re-initdb the
new cluster before continuing.

Performing Upgrade
------------------
Analyzing all rows in the new cluster                       ok
Freezing all rows in the new cluster                        ok
Deleting files from new pg_xact                             ok
Copying old pg_xact to new server                           ok
Setting oldest XID for new cluster                          ok
Setting next transaction ID and epoch for new cluster       ok
Deleting files from new pg_multixact/offsets                ok
Copying old pg_multixact/offsets to new server              ok
Deleting files from new pg_multixact/members                ok
Copying old pg_multixact/members to new server              ok
Setting next multixact ID and offset for new cluster        ok
Resetting WAL archives                                      ok
Setting frozenxid and minmxid counters in new cluster       ok
Restoring global objects in the new cluster                 ok
Restoring database schemas in the new cluster
                                                            ok
Copying user relation files
                                                            ok
Setting next OID for new cluster                            ok
Sync data directory to disk                                 ok
Creating script to analyze new cluster                      ok
Creating script to delete old cluster                       ok
Checking for extension updates                              ok

Upgrade Complete
----------------
Optimizer statistics are not transferred by pg_upgrade so,
once you start the new server, consider running:
    ./analyze_new_cluster.sh

Running this script will delete the old cluster's data files:
    ./delete_old_cluster.sh
```



### 2.2.3：配置文件还原

psql10.06的postgresql.conf ,pg_hba.conf 两个文件配置拷贝到10.22pgdata数据目录下面备份新的配置文件

开启日志信息参数

```bash
[postgres@s1 pgdata]$ mv postgresql.conf postgresql.conf-back
[postgres@s1 pgdata]$ mv pg_hba.conf pg_hba.conf-back 
[postgres@s1 pgdata]$ cp ../pgdata.old/postgresql.conf .
[postgres@s1 pgdata]$ cp ../pgdata.old/pg_hba.conf .

#开启日志参数
listen_addresses = '*'
port = 5432 
max_connections = 800
shared_buffers = 500MB
dynamic_shared_memory_type = posix
log_destination = 'csvlog'
log_directory = 'pg_log'
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
archive_mode = on
archive_command = 'test ! -f /home/postgres/archive/%f && cp %p /home/postgres/archive/%f'
log_truncate_on_rotation = on
logging_collector = on
log_min_duration_statement = 60
log_checkpoints = on
log_statement = 'mod'
log_timezone = 'PRC'
log_lock_waits = on
datestyle = 'iso, mdy'
timezone = 'PRC'
lc_messages = 'en_US.UTF-8'
lc_monetary = 'en_US.UTF-8'
lc_numeric = 'en_US.UTF-8'
lc_time = 'en_US.UTF-8'
default_text_search_config = 'pg_catalog.english'
```



### 2.2.4: 启动数据库

执行脚本前，需要先启动数据库pg_ctl  start

```bash

[postgres@s1 pgdata]$ pg_ctl start
waiting for server to start....2022-09-04 23:30:50.735 CST [121299] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2022-09-04 23:30:50.735 CST [121299] LOG:  listening on IPv6 address "::", port 5432
2022-09-04 23:30:50.738 CST [121299] LOG:  listening on Unix socket "/tmp/.s.PGSQL.5432"
2022-09-04 23:30:50.810 CST [121299] LOG:  redirecting log output to logging collector process
2022-09-04 23:30:50.810 CST [121299] HINT:  Future log output will appear in directory "pg_log".
 done
server started
```

执行脚本./analyze_new_cluster.sh，从运行日志来看，主要是创建统计信息

```bash
[postgres@s1 ~]$ ./analyze_new_cluster.sh 
This script will generate minimal optimizer statistics rapidly
so your system is usable, and then gather statistics twice more
with increasing accuracy.  When it is done, your system will
have the default level of optimizer statistics.

If you have used ALTER TABLE to modify the statistics target for
any tables, you might want to remove them and restore them after
running this script because they will delay fast statistics generation.

If you would like default statistics as quickly as possible, cancel
this script and run:
    "/home/postgres/pgsql10.22/bin/vacuumdb" --all --analyze-only

vacuumdb: processing database "postgres": Generating minimal optimizer statistics (1 target)
vacuumdb: processing database "template1": Generating minimal optimizer statistics (1 target)
vacuumdb: processing database "test": Generating minimal optimizer statistics (1 target)
vacuumdb: processing database "postgres": Generating medium optimizer statistics (10 targets)
vacuumdb: processing database "template1": Generating medium optimizer statistics (10 targets)
vacuumdb: processing database "test": Generating medium optimizer statistics (10 targets)
vacuumdb: processing database "postgres": Generating default (full) optimizer statistics
vacuumdb: processing database "template1": Generating default (full) optimizer statistics
vacuumdb: processing database "test": Generating default (full) optimizer statistics

Done
```

### 2.2.5: 进入数据库验证



```bash
[postgres@s1 ~]$ psql -U postgres 
psql (10.22)
Type "help" for help.

postgres=# select version();
                                                 version                        
                          
--------------------------------------------------------------------------------
--------------------------
 PostgreSQL 10.22 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 4.4.7 20120313 (
Red Hat 4.4.7-23), 64-bit
(1 row)

postgres=# \l
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileg
es   
-----------+----------+----------+-------------+-------------+------------------
-----
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres      
    +
           |          |          |             |             | postgres=CTc/post
gres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | postgres=CTc/post
gres+
           |          |          |             |             | =c/postgres
 test      | test     | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(4 rows)

postgres=# \c test;
You are now connected to database "test" as user "postgres".
test=# \dt
        List of relations
 Schema | Name | Type  |  Owner   
--------+------+-------+----------
 public | test | table | postgres
(1 row)

test=# select * from test;
 id  
-----
 111
 222
(2 rows)

test=# 

```

### 2.2.6：启动脚本

备份10.6开机启动脚本，拷贝10.22源码包里面的启动脚本到开机启动配置目录，进行重命名

```bash
#备份10.6启动脚本
[root@s1 archive]# mv /etc/init.d/postgres /etc/init.d/postgres-0905
[root@s1 start-scripts]# pwd
/home/postgres/soft/postgresql-10.22/contrib/start-scripts
[root@s1 start-scripts]# cp linux /etc/init.d/postgres
[root@s1 start-scripts]# chmod +x /etc/init.d/postgres


[root@s1 start-scripts]# cat /etc/init.d/postgres
#! /bin/sh

# chkconfig: 2345 98 02
# description: PostgreSQL RDBMS

# This is an example of a start/stop script for SysV-style init, such
# as is used on Linux systems.  You should edit some of the variables
# and maybe the 'echo' commands.
#
# Place this file at /etc/init.d/postgresql (or
# /etc/rc.d/init.d/postgresql) and make symlinks to
#   /etc/rc.d/rc0.d/K02postgresql
#   /etc/rc.d/rc1.d/K02postgresql
#   /etc/rc.d/rc2.d/K02postgresql
#   /etc/rc.d/rc3.d/S98postgresql
#   /etc/rc.d/rc4.d/S98postgresql
#   /etc/rc.d/rc5.d/S98postgresql
# Or, if you have chkconfig, simply:
# chkconfig --add postgresql
#
# Proper init scripts on Linux systems normally require setting lock
# and pid files under /var/run as well as reacting to network
# settings, so you should treat this with care.

# Original author:  Ryan Kirkpatrick <pgsql@rkirkpat.net>

# contrib/start-scripts/linux

## EDIT FROM HERE

# Installation prefix
prefix=/home/postgres/pgsql10.22

# Data directory
PGDATA="/home/postgres/pgdata"

# Who to run the postmaster as, usually "postgres".  (NOT "root")
PGUSER=postgres

# Where to keep a log file
PGLOG="$PGDATA/serverlog"

# It's often a good idea to protect the postmaster from being killed by the
# OOM killer (which will tend to preferentially kill the postmaster because
# of the way it accounts for shared memory).  To do that, uncomment these
# three lines:
#PG_OOM_ADJUST_FILE=/proc/self/oom_score_adj
#PG_MASTER_OOM_SCORE_ADJ=-1000
#PG_CHILD_OOM_SCORE_ADJ=0
# Older Linux kernels may not have /proc/self/oom_score_adj, but instead
# /proc/self/oom_adj, which works similarly except for having a different
# range of scores.  For such a system, uncomment these three lines instead:
#PG_OOM_ADJUST_FILE=/proc/self/oom_adj
#PG_MASTER_OOM_SCORE_ADJ=-17
#PG_CHILD_OOM_SCORE_ADJ=0

## STOP EDITING HERE

# The path that is to be used for the script
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# What to use to start up the postmaster.  (If you want the script to wait
# until the server has started, you could use "pg_ctl start" here.)
DAEMON="$prefix/bin/postmaster"

# What to use to shut down the postmaster
PGCTL="$prefix/bin/pg_ctl"

set -e

# Only start if we can find the postmaster.
test -x $DAEMON ||
{
        echo "$DAEMON not found"
        if [ "$1" = "stop" ]
        then exit 0
        else exit 5
        fi
}

# If we want to tell child processes to adjust their OOM scores, set up the
# necessary environment variables.  Can't just export them through the "su".
if [ -e "$PG_OOM_ADJUST_FILE" -a -n "$PG_CHILD_OOM_SCORE_ADJ" ]
then
        DAEMON_ENV="PG_OOM_ADJUST_FILE=$PG_OOM_ADJUST_FILE PG_OOM_ADJUST_VALUE=$PG_CHILD_OOM_SCORE_ADJ"
fi


# Parse command line parameters.
case $1 in
  start)
        echo -n "Starting PostgreSQL: "
        test -e "$PG_OOM_ADJUST_FILE" && echo "$PG_MASTER_OOM_SCORE_ADJ" > "$PG_OOM_ADJUST_FILE"
        su - $PGUSER -c "$DAEMON_ENV $DAEMON -D '$PGDATA' >>$PGLOG 2>&1 &"
        echo "ok"
        ;;
  stop)
        echo -n "Stopping PostgreSQL: "
        su - $PGUSER -c "$PGCTL stop -D '$PGDATA' -s"
        echo "ok"
        ;;
  restart)
        echo -n "Restarting PostgreSQL: "
        su - $PGUSER -c "$PGCTL stop -D '$PGDATA' -s"
        test -e "$PG_OOM_ADJUST_FILE" && echo "$PG_MASTER_OOM_SCORE_ADJ" > "$PG_OOM_ADJUST_FILE"
        su - $PGUSER -c "$DAEMON_ENV $DAEMON -D '$PGDATA' >>$PGLOG 2>&1 &"
        echo "ok"
        ;;
  reload)
        echo -n "Reload PostgreSQL: "
        su - $PGUSER -c "$PGCTL reload -D '$PGDATA' -s"
        echo "ok"
        ;;
  status)
        su - $PGUSER -c "$PGCTL status -D '$PGDATA'"
        ;;
  *)
        # Print help
        echo "Usage: $0 {start|stop|restart|reload|status}" 1>&2
        exit 1
        ;;
esac

exit 0
#测试
[postgres@s1 pgdata]$ /etc/init.d/postgres status
Password: 
pg_ctl: server is running (PID: 121430)
/home/postgres/pgsql10.22/bin/postgres
```



## 2.3：数据库还原方案

### 2.3.1：方案1-快照

服务器进行快照还原

### 2.3.2：方案2-版本还原

目前数据库版本10.22，由于升级有误先对当前版本还原到10.6版本

先停止数据库

10.22数据目录/home/postgres/pgdata 10.6数据备份目录/home/postgres/pgdata.old，将/home/postgres/pgdata进行重命名pgdata-10.22  重命名10.6版本数据备份目录pgdata.old pgdata

环境变量在当前用户下面更改.bash_profile /home/postgres/pgsql10.22 更改为/home/postgres/pgsql10.6，测对数据库进行版本验证，启动脚本还原，启动数据库，数据验证



```bash
#停止数据库
[postgres@s1 ~]$ pg_ctl stop
#还原老数据库数据目录
[postgres@s1 ~]$ mv pgdata pgdata-10.22
[postgres@s1 ~]$ mv pgdata.old pgdata
#环境变量还原
[postgres@s1 ~]$ vi .bash_profile 
export PATH=/home/postgres/pgsql10.6/bin:$PATH
export LD_LIBRARY_PATH=/home/postgres/pgsql10.6/lib:$LD_LIBRARY_PATH
export PGDATA=/home/postgres/pgdata
[postgres@s1 ~]$ source .bash_profile 
#启动数据库
#版本测试
[postgres@s1 ~]$ pg_ctl -V
pg_ctl (PostgreSQL) 10.6

#启动脚本还原  
[root@s1 start-scripts]# mv /etc/init.d/postgres /etc/init.d/postgres-back
[root@s1 start-scripts]# mv /etc/init.d/postgres-0905 /etc/init.d/postgres
[postgres@s1 ~]$ pg_ctl start
#验证
[postgres@s1 ~]$ psql -U postgres
psql (10.6)
Type "help" for help.

postgres=# \l
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileg
es   
-----------+----------+----------+-------------+-------------+------------------
-----
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres      
    +
           |          |          |             |             | postgres=CTc/post
gres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres      
    +
           |          |          |             |             | postgres=CTc/post
gres
 test      | test     | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(4 rows)
```

