# # 目录

## ## 设置运行目录
```
$ mkdir -m 777 -p /www/database/mysql/
```

## ## 工作目录

需要等待安装之后才能设置
```
$ chown -R mysql:mysql /www/database/mysql
```

# # 配置MySQL

主配置文件

```
$ vim /etc/my.cnf
```

## ## 主配置
```
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
; init_connect='SET NAMES utf8mb4' ;这句可以不加
...
; 修改路径
datadir = /www/database/mysql
...
innodb_data_home_dir = /www/database/mysql
innodb_data_file_path = ibdata1:200M;ibdata2:10M:autoextend
; 每个innodb的表的数据单独成文件，这个配置非常重要
innodb_file_per_table = 1
innodb_log_group_home_dir = /www/database/mysql
...

; 记录所有SQL语句 可以不设定
general_log = 1
general_log_file=/var/log/mysql-sql.log

; 其它配置，比如缓冲区、线程数、连接数等，酌情配置
```

## ## 主主集群配置

```

```



