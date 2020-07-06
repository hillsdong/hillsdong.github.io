## 01 基础架构
- 执行流程：客户端->连接器->分析器->优化器->执行器->存储引擎
- 连接器：show processlist 查看连接，sleep状态为空闲链接，wait_timeout控制连接超时，尽量用长连接，但长连接占内存，5.7后执行 mysql_reset_connetcion 释放
- 查询缓存: 建议配置 query_cache_type=DEMAND，即默认不使用，通过 SQL_CACHE 关键字使用 8.0后不支持
- 分析器：词法分析/语法分析/语义分析，表列不存在等检查也在此阶段
- 优化器：决定使用哪个索引/多表关联时表连接顺序等
- 执行器；先进行权限校验，慢查询日志中可看到扫描行数 rows_examined，和引擎真正扫描行数不一定相同
- 存储引擎：Innodb(5.5.5后默认)/MyISAM/Memory
- 作业：列是否存在，是在分析器阶段判断的

## 02 日志系统
- WAL技术（Innodb引擎）：Write-Ahead Logging，先写日志再写磁盘，类似先写粉板再写账本
- redolog： 固定文件数固定大小，循环写，物理日志，如4个1G文件， write pos 是当前位置，checkpoint 是当前要擦除的位置
- crash-safe，即通过 redolog 保证即使异常重启，记录也不会丢
- Server层：binlog，追加写，逻辑日志
- 更新语句流程：取某行->更新行数据->写入新行->更新到内存->写入 redolog，状态为 prepare -> 写入 binlog -> 提交事务 -> 更新 redolog 状态为 commit
- 两阶段提交：redolog 分为 prepare 状态和 commit 状态，如何不依赖 binlog 保证一致性？
- 作业：根据业务容忍恢复时间来决定备份周期

## 03/08 事务隔离
- 事务：ACID（Atomicity原子性/Consistency一致性/Isolation隔离性/Durability持久性）
- 问题：脏读（dirty read）/不可重复读（non-repeatable read）/幻读（phantom read）
- 隔离级别：读未提交（read uncommitted）/读提交（read committed）/可重复读（repeatable read）/串行化（serializable）
- 多版本并发控制（MVCC）: 同一条记录在系统中存在多个版本，不同时刻的事务对应不同的视图（read-view），通过回滚得到不同版本
- 长事务危害：回滚段占用内存，占用锁资源
- 事务启动方式：set autocommit=0时，所有事务均需手动commit；=1时，默认自动commit，使用begin显式启动事务时需手动commit
- 作业：如何避免长事务？
- 开发端：set autocommit=1；避免不必要的只读事务；set max_execution_time=xxx
- 数据库端：监控 information_schema.Innodb_trx；pt-kill 工具；general_log 分析；innodb_undo_tablespaces=2
- 当前读： 事务中更新数据都是先读后写，不遵循一致性视图，select 加锁也是当前读
- 表结构没有行数据，没有row trx_id，只能遵循当前读，不支持可重复读


## 04/05 索引
- 哈希表：哈希算法+数组+链表，等值查询性能高，范围查询性能低
- 有序数组：等值查询和范围查询性能都很高，更新成本高
- 二叉搜索树：搜索效率最高，树高较高导致磁盘请求次数多
- N叉数；InnoDB整数字段索引的 N 差不多为1200，应用广泛
- 跳表/LSM树
- 
- InnoDB 表使用 B+ 树索引模型，分为主键索引/非主键索引
- 主键索引也叫聚簇索引 clustered index，叶子节点存放数据页，包括多个整行数据
- 非主键索引也叫二级索引 secondary index，叶子节点存放主键
- 自增主键优点：新增数据性能好，直接追加即可；二级索引占用空间小
- 自增主键索引建议设置为 bigint unsigned
- 作业：先重建非主键索引再重建主键索引不合理，非主键索引包含主键索引
- 重建索引可减少页空洞，减少空间占用
- 
- 覆盖索引：索引覆盖了查询所需字段，可以避免回表，减少树的搜索次数，提升性能
- 前缀索引：联合索引的最左N个字段，或字符串索引的最左N个字符，都可以应用索引
- 索引下推：5.6之后，索引遍历过程中，对索引中包含的字段先做判断，减少回表次数
- 作业：非主键索引会把主键索引加在后面，当 ab 为主键时, 索引 c / ca / cab 是一样的

## 06/07 锁
- 设计初衷：处理并发问题，合理控制资源访问
- 分为：全局锁/表级锁/行锁
- 全局锁：使用 FTWRL 语句添加，堵塞 数据更新语句/数据定义语句/更新类事务的提交语句
- 全库逻辑备份：最早使用全局锁，现可使用 mysqldump --single-transaction 在可重复读隔离级别下启动事务
- 不建议使用 set global readonly=true 做备份，会用来做备库判断，且异常不会自动恢复
- 表级别锁：分为表锁（lock tables T read/write）/MDL 元数据锁
- 对表做增删改查操作的时候，加 MDL 读锁，对表做结构变更操作的时候，加 MDL 写锁
- MDL 读锁间不冲突，读写锁间/写锁间冲突
- MDL 写锁被堵塞后，以后的 MDL 读锁都会被堵塞，因此给小表加字段也有可能堵塞库，可设置等待时间解决
- 行锁： 两个更新事务不能同时更新一行
- 两阶段锁： 在需要的时候加上，但等到事务结束的时候才释放
- 减少锁等待： 把事务中，将最可能造成锁冲突的锁往后放
- 解决死锁： 配置锁等待超时/配置死锁检测/中间件同行更新等待/将单行拆为多行

## 18 函数计算用不上索引
- 显式函数
    ```
    mysql> select count(*) from tradelog where month(t_modified)=7;
    ```
- 隐式类型转换函数，tradeid为varchar，字符串和数字比较时，会将字符串转换为数字
    ```
    mysql> select * from tradelog where tradeid=110717;
    ```
- 隐式字符编码转换函数，d.tradeid为utf8编码，l.tradeid为utf8mb4编码
    ```
    mysql> select d.* from tradelog l, trade_detail d where d.tradeid=l.tradeid and l.id=2;
    ```
- DDL (Data Definition Language) 数据库模式定义语言 CREATE ALTER DROP TRUNCATE COMMENT RENAME
  DML（Data Manipulation Language）数据操纵语言 SELECT INSERT UPDATE DELETE MERGE CALL EXPLAIN PLAN LOCK TABLE
  DCL（Data Control Language）数据库控制语言 GRANT 授权 REVOKE 取消授权
  TCL（Transaction Control Language）事务控制语言 SAVEPOINT 设置保存点 ROLLBACK  回滚 SET TRANSACTION
- EXPLAIN
  type：ALL < index < range ~ index_merge < ref < eq_ref < const < system
  extra：Using filesort / Using index / Using temporary
- 字符编码转换函数 CONVERT(l.tradeid USING utf8)
- 类型转换函数 CAST(tradid AS signed int)

## 11 字符串加索引
- 完整索引 占空间/没毛病
- 前缀索引 left(field, n) 节省空间，增加扫面行数，不能使用覆盖索引
- 倒序索引 reverse(field) 解决前缀索引区分度不够的问题，不支持范围扫描
- 创建hash字段索引 crc32(field) 性能稳定，占用额外存储和计算，不支持范围扫描
- 作业：字符串转换为整型索引

## 16 order by
- 全字段排序 sort_buffer_size 排序内存空间
    ```
    /* 打开 optimizer_trace，只对本线程有效 */
    SET optimizer_trace='enabled=on'; 
    /* @a 保存 Innodb_rows_read 的初始值 */
    select VARIABLE_VALUE into @a from  performance_schema.session_status where variable_name = 'Innodb_rows_read';
    /* 执行语句 */
    select city, name,age from t where city='杭州' order by name limit 1000; 
    /* 查看 OPTIMIZER_TRACE 输出 */
    SELECT * FROM `information_schema`.`OPTIMIZER_TRACE`\G
    /* @b 保存 Innodb_rows_read 的当前值 */
    select VARIABLE_VALUE into @b from performance_schema.session_status where variable_name = 'Innodb_rows_read';
    /* 计算 Innodb_rows_read 差值 */
    select @b-@a;
    ```
    filesort_summary.examined_rows 参与排序行数
    filesort_summary.number_of_tmp_files 临时文件数量
    filesort_summary.sort_mode packed_additional_fields 紧凑处理 / sort_key / rowid
    internal_tmp_disk_storage_engine MyISAM / InnoDB
- rowid 排序 max_length_for_sort_data 最大排序数据长度（B）
- 如果内存够，就多用，尽量少访问磁盘
- 索引排序，不用排序
- 全覆盖索引排序，不用回表查询
- 作业：WHERE IN 排序时，可利用索引排序，然后在程序中归并

## 45 自增ID用完
- 表的自增ID int 4字节32位 bigint 8字节64位，用完，取最大ID，报主键冲突
- InnoDB系统自增rowid 6字节48位，代码中是8字节64位，用完，归0，覆盖数据
- Xid binlog 概率极小 ?
- InnoDB trx_id ?
- thread_id 4字节32位
    ```
    do {
    new_id= thread_id_counter++;
    } while (!thread_ids.insert_unique(new_id).second);

    ```
- table_id / binlog文件序号 等

## 43 分区表
```
CREATE TABLE `t` (
  `ftime` datetime NOT NULL,
  `c` int(11) DEFAULT NULL,
  KEY (`ftime`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
PARTITION BY RANGE (YEAR(ftime))
(PARTITION p_2017 VALUES LESS THAN (2017) ENGINE = InnoDB,
 PARTITION p_2018 VALUES LESS THAN (2018) ENGINE = InnoDB,
 PARTITION p_others VALUES LESS THAN MAXVALUE ENGINE = InnoDB);
insert into t values('2017-4-1',1),('2018-4-1',1);
```
- 引擎层行为？
- 优势：业务透明/方便清理历史数据（alert table drop partition）
- 劣势：第一次打开分区表访问所有分区/server层共用MDL锁？，引擎层访问必要分区
- open_files_limit限制打开表的个数/innodb_open_files超过会关掉之前打开的文件
- 通用分区策略/本地分区策略（5.7.9后innodb引擎）不会为超过打开文件上线而报错
- range分区/hash分区/list分区
- 分区不是越细越好/不要提前预留太多
- 分库分表中间件更优

## 42 flush privileges
```
create user 'ua'@'%' identified by 'pa';  //mysql.user acl_users
grant all privileges on *.* to 'ua'@'%' with grant option; //mysql.user acl_users 线程
revoke all privileges on *.* from 'ua'@'%'; //mysql.user acl_users
grant all privileges on db1.* to 'ua'@'%' with grant option; //mysql.db acl_dbs 全局
create table db1.t1(id int, a int);
grant all privileges on db1.t1 to 'ua'@'%' with grant option; //mysql.tables_priv column_priv_hash 全局
GRANT SELECT(id), INSERT (id,a) ON mydb.mytbl TO 'ua'@'%' with grant option; //mysql.column_priv column_priv_hash 全局
```
- 不要给用户设置全部权限
- 不要用DML语句操作系统权限表，否则跟flush privileges
- grant后不用跟flush privileges

## 41 快速复制表
mysql -uroot -pxiaobaitu2 </var/lib/mysql/test.sql

```
create database db1;
use db1;

create table t(id int primary key, a int, b int, index(a))engine=innodb;
delimiter ;;
  create procedure idata()
  begin
    declare i int;
    set i=1;
    while(i<=1000)do
      insert into t values(i,i,i);
      set i=i+1;
    end while;
  end;;
delimiter ;
call idata();

create database db2;
create table db2.t like db1.t
```
- insert...select...
- mysqldump / source
```
mysqldump -h$host -P$port -u$user --add-locks=0 --no-create-info --single-transaction  --set-gtid-purged=OFF db1 t --where="a>900" --result-file=/client_tmp/t.sql
// --no-create-info 不导出表结构
// --add-locks=0 输出文件不加表写锁
// --single-transaction 不加表锁
//--set-gtid-purged 不输出gtid相关
mysqldump -u root -p --databases a.geekbang.org b.geekbang.org > xxx.sql
mysql -h127.0.0.1 -P13000  -uroot db2 -e "source /client_tmp/t.sql"
```
- select ... into outfile ... / load data infile ... into table ...
```
select * from db1.t where a>900 into outfile '/server_tmp/t.csv'; //不会生成表结构, 受限secure_file_priv指定目录
load data infile '/server_tmp/t.csv' into table db2.t;
//load data infile 服务端文件，受限secure_file_priv指定目录
//load data local infile 客户端文件，有访问权限即可
```
- 物理拷贝 >=5.6 transportable tablespace
执行 create table r like t，创建一个相...

## 22 饮鸩止渴
- 短链接风暴 max_connections 1. kill connection :id (information_schema.innodb_trx) 2. --skip-grant-tables, 外网不可访问
- 慢查询 1. 加索引 set sql_bin_log=off alert table 2. query_rewrite 3. force index
```
mysql> insert into query_rewrite.rewrite_rules(pattern, replacement, pattern_database) values ("select * from t where id + 1 = ?", "select * from t where id = ? - 1", "db1");
call query_rewrite.flush_rewrite_rules();
```
- 慢查询自测 slow log打开，设置long_query_time=0，测试，留意Rows_examined
- QPS突增 1. 业务白名单 2. 单独数据库账户 3. 查询重写为 select 1

## 23 数据不丢
- WAL（Write-ahead logging）预写式日志，redo log 和 binlog 落盘
- binlog落盘 binlog cache -> binglog files(write) -> disk(fsync)  sync_binlog(0/1/N[100-1000])
- redo log落盘 redo log buffer -> FS page cache(write) -> disk(fsync) innodb_flush_log_at_trx_commit(0/1/2) 1s一次/超过innodb_log_buffer_size一半/并行事务提交
- 双1配置 relod prepare阶段落盘，binlog阶段落盘
- group commit机制 binlog_group_commit_sync_delay/binlog_group_commit_no_delay_count
- 两阶段提交：redo log prepare(write) -> binlog(write) -> reldo log prepare (fsync) -> binlog(fsync) -> redo log commit(write)

## 24 主备一致
- 基本原理 binlog
- binlog三种格式 binlog_format statement(不一致风险) / row（文件大，可用于恢复数据，binlog_row_image=FULL|MINIMAL） / mixed
- XID?
```
mysql> show binlog events in 'master.000001';
mysqlbinlog  -vv data/master.000001 --start-position=8900;
mysqlbinlog master.000001  --start-position=2738 --stop-position=2973 | mysql -h127.0.0.1 -P13000 -u$user -p$pwd;
```
- 循环复制问题，server id
- 作业：修改 server id 或切换 server
```
stop slave；
CHANGE MASTER TO IGNORE_SERVER_IDS=(server_id_of_B);
start slave;
```

## 25 高可用
- 主备延迟 seconds_behind_master / show slave status可看 / 网络延迟一般很短，主要延迟来自备库消费中转日志(relay log)
- 主备延迟原因 备库主机配置低 / 备库压力大（读压力），可设置多个备库，或数据分析交给Hadoop等 / 大事务（批量删除/大表DDL），gh-ost方案
- 主备切换流程 可靠性优先策略 seconds_behind_master<5 -> Master readonly=true -> seconds_behind_master==0 -> Slave readonly=false -> 切 会导致不可用
- 主备切换流程 可用性优先厕率 Slave readonly=false -> 切 会导致数据不一致
- 作业：延迟时间线性增长，备库完全堵住了，可能是大事务，也可能是长事务

## 26 备库的并行复制能力
- Mysql5.5 自写 按表分发，key为库名+表名 / 按行分发 ，key为库名+表名+主键值+其他唯一索引值（binlog格式必须为row，必须有主键，不能有外键）
- Mysql5.6 按库分发，key为库名
- MariaDB 按commitid分发（group commit）
- Mysql5.7 slave-parallel-type(DATABASE[按库分发] | LOGICAL_CLOCK [prepare阶段])
- Mysql 5.7.22 binlog-transaction-dependency-tracking(COMMIT_ORDER[prepare阶段] | WRITETEST[按行分发] | WRITETEST_SESSION[按行分发+线程ID])
- 作业：单线程插入数据的主库，没有group commit，应选择WRITETEST模式

## 27 主备切换
- 寻找位点
```
CHANGE MASTER TO 
MASTER_HOST=$host_name 
MASTER_PORT=$port 
MASTER_USER=$user_name 
MASTER_PASSWORD=$password 
MASTER_LOG_FILE=$master_log_name 
MASTER_LOG_POS=$master_log_pos  

mysqlbinlog File --stop-datetime=T --start-datetime=T
```
- 处理错误： slave_skip_errors='1032,1062' 删除找不到，唯一键冲突 / sql_slave_skip_counter=1
- GTID(Global Transaction Identifier) server_uuid:gno gtid_mode=on enforce_gtid_consistency=on
```
CHANGE MASTER TO 
MASTER_HOST=$host_name 
MASTER_PORT=$port 
MASTER_USER=$user_name 
MASTER_PASSWORD=$password 
master_auto_position=1 
```
- 在线DDL 1. 主stop slave 2. 备执行DDL 3. 找到GTID  4. 主增加GTID 5. 切 6.重复1-4
```
set GTID_NEXT="server_uuid_of_Y:gno";
begin;
commit;
set gtid_next=automatic;
start slave;
```
- 作业：show global variables like 'gtid_purged' / reset master / set global gtid_purged="" / start slave



## 配置
sort_buffer_size
max_length_for_sort_data
open_files_limit
innodb_open_files
max_connections
sql_bin_log
long_query_time
binlog_format
binlog_row_image
binlog_cache_size
sync_binlog
innodb_flush_log_at_trx_commit
binlog_group_commit_sync_delay
binlog_group_commit_no_delay_count
slave-parallel-type
binlog-transaction-dependency-tracking

## 数据
information_schema.OPTIMIZER_TRACE
information_schema.innodb_trx
query_rewrite.rewrite_rules
mysql.user
mysql.db
mysql.tables_priv
mysql.column_priv

## 工具
- explain
- mysqldump
- source
- mysqlbinlog
- err log 
- slow log
- 