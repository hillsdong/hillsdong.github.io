## 指定用户运行脚本
```
su -s /bin/bash -c '/usr/local/bin/php run.php 1 1' www
```

## shell批量替换
```
sed -i 's/chinalove99.net/juaicheng.com/g' `find . -name "*.php" | xargs grep -rl "chinalove99.net"`
```

## mysql导出导入
```
mysqldump -u root -p --databases a.geekbang.org b.geekbang.org > xxx.sql
mysql -uroot -pxiaobaitu2 </var/lib/mysql/test.sql
```

## tar压缩解压
```
tar -zcf a.tar.gz a
tar -zxf a.tar.gz
```

## yum 回滚
```
sudo yum downgrade Geekbang-App-Be-Time-Stable-0.2-244.el7.centos.x86_64
```