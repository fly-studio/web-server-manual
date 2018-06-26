# # 配置MariaDB

主配置文件
```
$ vim /etc/my.cnf.d/server.cnf
```

```
[mysqld]
datadir=/www/database/mysql
socket=/var/lib/mysql/mysql.sock
#default-character-set=utf8mb4 ;这句话MariaDB无法识别
character_set_server=utf8mb4
slow_query_log=on
slow_query_log_file=/var/log/mysqld-slow.log
long_query_time=2
```
创建慢日志
> MariaDB无法自己创建该文件

```
$ touch /var/log/mysqld-slow.log
$ chmod 666 /var/log/mysqld-slow.log
```

