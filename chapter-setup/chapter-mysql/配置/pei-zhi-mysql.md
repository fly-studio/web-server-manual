# # 配置MySQL

主配置文件
```
$ vim /etc/my.cnf
```

注意：按照下面的键名修改你的配置文件，文件中存在其它的键可以保留

```
[mysqld]

; character
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
; init_connect='SET NAMES utf8mb4' ;这句可以不加


; 主要配置
default_storage_engine=innodb
datadir = /www/database/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
bind-address=0.0.0.0

; innodb
innodb_data_home_dir = /www/database/mysql
innodb_data_file_path = ibdata1:200M;ibdata2:10M:autoextend
; 每个innodb的表的数据单独成文件，这个配置非常重要
innodb_file_per_table = 1
innodb_log_group_home_dir = /www/database/mysql


; 开启BinLog
log_bin=mysql-bin
binlog_format=ROW
binlog_row_image=FULL
explicit_defaults_for_timestamp=true
; 在BinLog打开的时候需要设置一个ID，集群内唯一
server-id=10


; 记录所有SQL语句，如果有BinLog，则可以不设定
general_log = 1
general_log_file=/var/log/mysql-sql.log

; 其它配置，比如缓冲区、线程数、连接数等，酌情配置

[mysql_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

