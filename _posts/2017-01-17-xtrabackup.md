---
layout: post
title: Xtrabackup双机热备份MySQL数据库
tags: [xtrabackup, mysql]
keywords: [系统运维, MySQL数据库, Xtrabackup, 双机热备份]
---

Percona XtraBackup可以说是一个相对完美的免费开源数据备份工具，支持**在线无锁表同步复制**和可**并行高效率的安全备份恢复机制**, 相比 `mysqldump` 来说确实让人眼前一亮。`xtrabackup` 包含两个主要的工具，即 `xtrabackup` 和 `innobackupex`，二者区别如下：

1. `xtrabackup` 只能备份 `innodb` 和 `xtradb` 两种引擎的表，而不能备份 `myisam` 引擎的表；
2. `innobackupex` 是一个封装了xtrabackup的Perl脚本，支持同时备份innodb和myisam，但在对myisam备份时需要加一个全局的读锁。**还有就是myisam不支持增量备份。**


**MySQL主从同步原理**

`MySQL`主从同步是在MySQL主从复制(Master-Slave Replication)基础上实现的，通过设置在 `Master MySQL` 上的 `binlog` (使其处于打开状态)，`Slave MySQL` 上通过一个I/O线程从 `Master MySQL` 上读取 `binlog`，然后传输到 `Slave MySQL` 的中继日志中，然后 `Slave MySQL` 的SQL线程从中继日志中读取中继日志，然后应用到 `Slave MySQL` 的数据库中。这样实现了主从数据同步功能。

**XtraBackup备份原理**

`innobackupex` 在后台线程不断追踪 `InnoDB` 的日志文件，然后复制 `InnoDB` 的数据文件。数据文件复制完成之后，日志的复制线程也会结束。这样就得到了不在同一时间点的数据副本和开始备份以后的事务日志。完成上面的步骤之后，就可以使用 `InnoDB` 崩溃恢复代码执行事务日志（redo log），以达到数据的一致性。

_备份分为两个过程_：

- `backup`，备份阶段，追踪事务日志和复制数据文件（物理备份）。
- `preparing`，重放事务日志，使所有的数据处于同一个时间点，达到一致性状态。

### 操作记录 ###

1. 主、从服务器上都搭好MySQL服务，从服务器上MySQL版本大于等于主服务器，最好完全一致; 可以使用下面的命令查看当前MySQL的版本

```
$ mysql -V
mysql  Ver 14.14 Distrib 5.5.31, for Linux (x86_64) using readline 5.1
```

2. 安装 `percona-xtrabackup`

主从服务器上分别安装XtraBackup，根据官方网站指导使用打包好的二进制，选择最新的稳定版2.4：

```
# master & slave
wget https://repo.percona.com/apt/percona-release_0.1-3.$(lsb_release -sc)_all.deb
sudo dpkg -i percona-release_0.1-3.$(lsb_release -sc)_all.deb
sudo apt-get update
sudo apt-get install percona-xtrabackup-24
```

3. 停掉从服务器上MySQL服务，备份原有数据库，并删除原有数据库内容

```
mysqldump -u$USER -p$PASSWORD -h127.0.0.1 -P3306 --routines \
  --default-character-set=utf8 --locak-all-tables --add-drop-database -A \
  db.all.sql
sudo service mysql stop
sudo cd /var/lib/mysql
# 下面这句千万别打错了，后果会很严重
sudo rm -rf ./*
```

4. 配置MySQL打开主从同步功能

- 主服务器上编辑 `/etc/mysql/my.conf` 文件：

```
[mysqld]
# 注意主从之间的server-id不能相同
server-id    = 1
log_bin      = /var/log/mysql/mysql-bin.log
```

如果主服务器上MySQL是已经上线的系统，需要重启一下（实测 `/etc/init.d/mysql reload` 不起作用）：

```
sudo service mysql restart
```

- 从服务器上编辑 `/etc/mysql/my.conf` 文件：

```
[mysqld]
# 注意主从之间的server-id不能相同
server-id    = 2
# 最好设置从服务器为只读
# 注意：即使这里设置了只读，使用具有super权限的用户登录，也还是可以做写操作的
read_only    = ON
```
- 查询主从服务器状态：

```
mysql -u USER -p PASSWD -e "show global variables like 'server-id';"
    +---------------+-------+
    | Variable_name | Value |
    +---------------+-------+
    | server_id     | 1     |
    +---------------+-------+

mysql -u USER -p PASSWD -e "show global variables like 'log_bin';"
    +---------------+-------+
    | Variable_name | Value |
    +---------------+-------+
    | log_bin       | ON    |
    +---------------+-------+
```

5. 主服务器上执行备份操作

```
sudo innobackupex --defaults-file=/etc/mysql/my.cnf --user=USER --password \
  --parallel=4 /tmp/mybackup
```

命令输出的最后几行通常类似这样：

```
innobackupex: Backup created in directory '/tmp/mybackup/2016-04-26_17-41-51'
innobackupex: MySQL binlog position: filename 'mysql-bin.000003', position 1946
111225 00:00:53 innobackupex: completed OK!
```

命令执行完在 `/tmp/mybackup` 目录下生成的 `2016-04-26_17-41-51` 目录，里面存储的是备份的数据，下一步要传输到从服务器上的即是这个文件夹。
输出中的`MySQL binlog position: filename 'mysql-bin.000003', position 1946` 里面的两个数字，要记录以下，后面恢复到从服务器上的时候要用到。

6. 传输并同步备份数据

- 读取备份数据需要ROOT权限，下面的命令需要使用sudo执行。

```
sudo scp -r /tmp/mybackup/2016-04-26_17-44-49 USER@SLAVE:/tmp/mybackup/2016-04-26
```

- 在从服务器上执行：

```
sudo innobackupex --apply-log /tmp/mybackup/2016-04-26
```

7. 从服务器上恢复备份数据

```
# 恢复数据
sudo innobackupex --defaults-file=/etc/mysql/my.cnf --user=USER --password \
  --copy-back /tmp/mybackup/2016-04-26/
# 需要恢复权限给mysql
sudo chown -R mysql:mysql /var/lib/mysql
# 启动MySQL
sudo service mysql start
```

8. 主服务器上授权同步帐号

```
mysql -u USER -p PASSWD -h HOST -P PORT
> grant replication slave on *.* to 'slave'@'10.10.16.24' identified by 'slave_passport';
> flush privileges;
>
> select distinct concat('User: ''',user,'''@''',host,''';') as query from mysql.user;
>
```

9. 配置从服务器开启同步

```
mysql -u USER -p PASSWD -h HOST -p PORT
> change master to
> master_host = '10.10.16.51',
> master_user = 'slave',
> master_password = 'slave_password',
> master_port = 3306,
> master_log_file = 'mysql-bin.000003',
> master_log_pos = 1946;
>
> start slave;
```

- 查看主库同步状态：

```
mysql -u USER -p PASSWD -h MASTER_HOST -P MASTER_PORT \
  -e "show master status \G;"
mysql -u USER -p PASSWD -h MASTER_HOST -P MASTER_PORT \
  -e "show processlist \G;" | grep -i 'master'
```

- 查看从库同步状态：

```
mysql -u USER -p PASSWD -h SLAVE_HOST -P SLAVE_PORT \
  -e "show slave status \G;"
mysql -u USER -p PASSWD -h SLAVE_HOST -P SLAVE_PORT \
  -e "show processlist \G;" | egrep -i '(master|slave)'
```

### 主从复制心跳和连接超时 ###

实际运行过程中, 由于没有专门的人做运维, 从服务器也只是起备份作用, 偶尔发现从服务器已经没有跟主服务器同步, 数据滞后了很长时间了, 估计是跟公司网络环境不大稳定有关系.

在网上了解到MySQL 5.5以上版本的主从复制还有一个心跳功能, 参考这里: [MySQL运维-主从复制心跳](http://blog.csdn.net/JesseYoung/article/details/42914577). 果断打开心跳功能.

连接从服务器, 执行下面指令配置心跳周期和连接超时:

```
mysql> stop slave;
mysql> change master to master_heartbeat_period = 10;
mysql> set global slave_net_timeout = 25;
mysql> start slave;
```

可以通过以下命令检查心跳状态:

```
mysql -uUSER -pPASSWORD -hHOST -PPORT -e "show status like 'slave%';"
    +----------------------------+--------+
    | Variable_name              | Value  |
    +----------------------------+--------+
    | Slave_heartbeat_period     | 10.000 |
    | Slave_last_heartbeat       |        |
    | Slave_open_temp_tables     | 0      |
    | Slave_received_heartbeats  | 0      |
    | Slave_retried_transactions | 0      |
    | Slave_running              | ON     |
    +----------------------------+--------+
```


### MySQL主从切换 ###

```
#查看主库状态
show processlist;
Master has sent all binlog to slave; waiting for binlog to be updated
show master status \G

#从库停止 IO_THREAD 线程
stop slave IO_THREAD;
show processlist;
Slave has read all relay log; waiting for the slave I/O thread to update it
show slave status \G

#从库切换为主库
stop slave;
reset master;
reset slave all;
show master status \G

#激活帐户
SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;
GRANT REPLICATION SLAVE ON *.* TO 'slave_passport'@'10.10.16.51' IDENTIFIED BY 'slave_passport';
FLUSH PRIVILEGES;

#切换原有主库为从库
reset master;
reset slave all;

CHANGE MASTER TO
MASTER_HOST='10.10.16.24',
MASTER_USER='slave_passport',
MASTER_PASSWORD='slave_passport',
MASTER_PORT=3306,
MASTER_LOG_FILE='mysql-bin.000001',
MASTER_LOG_POS=804497686;

#检查主库
SHOW PROCESSLIST;
show master status \G

#启动从库
SHOW PROCESSLIST;
start slave;
show slave status \G
```

参考文献：

- [XtraBackup不停机不锁表搭建MySQL主从同步实践](http://wsgzao.github.io/post/xtrabackup/)

- [XtraBackup热备份MySQL主从同步笔记](http://hshsh.me/post/2016-04-26-mysql-hot-backup-with-xtrabackup/)
