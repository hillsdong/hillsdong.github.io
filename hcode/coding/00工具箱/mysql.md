```
/*常规设置*/
CREATE USER 'tds_geekbang'@'%' IDENTIFIED BY 'c2z21uA2_2c2B';

/*高级设置*/

/*服务器权限设置*/
GRANT PROCESS, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'tds_geekbang'@'%';

/*数据库权限设置*/
GRANT SELECT,EVENT,INSERT,UPDATE,REFERENCES,DELETE,CREATE,DROP,ALTER,INDEX,TRIGGER,CREATE VIEW,SHOW VIEW,EXECUTE,ALTER ROUTINE,CREATE ROUTINE,CREATE TEMPORARY TABLES,LOCK TABLES ON `test\_dailysearch\_geekbang\_org`.* TO 'tds_geekbang'@'%';

GRANT SELECT,LOCK TABLES,SHOW VIEW ON `test\_dailysearch\_geekbang\_org`.* TO 'geekbang'@'%';
```


```
DATE_FORMAT(FROM_UNIXTIME(chattime),’%Y-%m-%d’)
```