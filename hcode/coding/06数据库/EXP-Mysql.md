## 主备搭建
- 主库创建测试数据库
    ```
    CREATE DATABASE book_klib_cn;
    use book_klib_cn;
    CREATE TABLE book (
        id INT PRIMARY KEY AUTO_INCREMENT,
        name VARCHAR(64) NOT NULL DEFAULT '' COMMENT '书名',
        ctime BIGINT UNSIGNED NOT NULL
    ) ENGINE=innodb CHARSET=utf8mb4 COMMENT='图书';
    INSERT INTO book(name,ctime) VALUES ("追风筝的人",UNIX_TIMESTAMP(NOW()));
    ```
- 主库创建同步用户并授权
    ``` 
    create user 'slave'@'%' identified by 'slave123';
    grant replication slave, replication client on *.* to 'slave'@'%';
    ```
- 主库状态确认
    ```
    show master status;
    ```
- 备库配置
    ```
    change master to master_host='db1', master_user='slave', master_password='slave123', master_port=3306, master_log_file='binlog.000001', master_log_pos=156, master_connect_retry=30;
    ```
- 备库启动并确认状态
    ```
    start slave;
    show slave status;
    ```

## 常见速率
```
T his group of numbers is from a presentation Jeff Dean gave at a Engineering All-Hands Meeting at Google.

L1 cache reference 0.5 ns
Branch mispredict 5 ns
L2 cache reference 7 ns
Mutex lock/unlock 100 ns
Main memory reference 100 ns
Compress 1K bytes with Zippy 10,000 ns
Send 2K bytes over 1 Gbps network 20,000 ns
Read 1 MB sequentially from memory 250,000 ns
Round trip within same datacenter 500,000 ns
Disk seek 10,000,000 ns
Read 1 MB sequentially from network 10,000,000 ns
Read 1 MB sequentially from disk 30,000,000 ns
Send packet CA->Netherlands->CA 150,000,000 ns
```

## 变量
```
wait_timeout #连接超时，默认 28800s
query_cache_type #查询缓存使用方式，DEMAND 为默认不使用
innodb_flush_log_at_trx_commit #redolog 落盘配置，1为每次都落盘
sync_binlog #binlog 落盘配置，1为每次都落盘
binlog_format #binlog 格式，STATEMENT/ROW/MIX
transaction_isolation #事物隔离级别，默认为可重复读 REPEATABLE-READ
innodb_undo_tablespaces #innodb 回滚表空间
innodb_lock_wait_timeout #锁等待超时时间
innodb_deadlock_detect #死锁检测 ON/OFF

```

## explain
```
rows_examined #扫描行数
```

## 命令
- 配置变量/状态变量查询
    ```
    show processlist; # 查看连接
    show variables like '%binlog%';
    show variables where Variable_name like 'log%' and value='ON';
    show global variables;
    show session/local variables;
    show status;
    # 执行时间超过60s的事务
    select * from information_schema.innodb_trx where TIME_TO_SEC(timediff(now(),trx_started))>10;
    # 索引使用情况
    performance_schema.table_io_waits_summary_by_index_usage
    # 锁查询
    select THREAD_ID,EVENT_ID,OBJECT_NAME,INDEX_NAME,LOCK_TYPE,LOCK_MODE,LOCK_DATA from performance_schema.data_locks;
    ```
-  客户端命令
    ```
    mysql_reset_connetcion #重置连接，可释放连接占用的内存
    begin/start transaction #启动事务
    commit/rollback #提交/回滚事务
    commit work and chain #提交并启动下一个事务
    set autocommit=0 #关闭自动提交，未主动提交会触发长事务
    set max_execution_time=xx #事务超时时间
    alter table T drop index k; #删除索引k
    alter table T add index(k); #增加索引k
    alter table T drop primary key; #删除主键索引
    alter table T add primary key(id); #增加主键索引
    alter table T engine=InnoDB; #重建索引
    flush tables with read lock; #FTWRL，加全局锁，可用于不支持可重复读的数据引擎备份
    set global readonly=true; #设置全库只读
    lock tables T read/write; #表锁
    unlock tables ...; #释放表锁
    alter table T nowait add column ... #不等待表级写锁
    alter table T wait N add column ... #等待 N 表级写锁
    show create table T; #查询建表语句
    select * from T where id=1 lock in share mode; #加读锁（共享锁）S，可实现当前读
    select * from T where id=1 for update; #加写锁（排他锁）X，可实现当前读
    ```
- mysql工具
    ```
    mysql -h$ip -P$port -u$user -p
    mysqldump --single-transaction #启动事务获取一致性视图
    ```
## log
- error log
- slow log
- general_log
- binlog

## 概念
- RTO（恢复目标时间），从数据库备份恢复所用时间
- MDL meta data lock，元数据锁
- DML 
- DDL 

## MyISAM/InnoDB 对比
- 事务： MyISAM 不支持 / InnoDb 支持
- 行锁： MyISAM 不支持 / InnoDb 支持
- 表总行数： MyISAM 记录在表中 / InnoDb 每次都计算

## 工具
- Percona 的 pt-kill