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

注意：按照下面的键名修改你的配置文件，文件中存在其它键可以保留

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
explicit_defaults_for_timestamp=true
; 在BinLog打开的时候需要设置一个ID，集群内唯一
server-id=10

; 记录所有SQL语句，如果有BinLog，则可以不设定
general_log = 1
general_log_file=/var/log/mysql-sql.log

; 其它配置，比如缓冲区、线程数、连接数等，酌情配置

; 添加到[mysqld]末尾
!includedir  /etc/my.cnf.d/

[mysql_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```


此时数据库已经可以正常启动

启动之后，需要创建一个有CRUD权限的用户名，如何创建[请参考MySQL密码设置](/chapter-setup/chapter-mysql/yong-hu-he-mi-ma.md)


## ## 主主集群配置

```
$ vim /etc/my.cnf.d/wsrep.cnf
```

```
[mysqld]

; 必须设置为2 交错模式，才能在集群自增的时候不冲突
innodb_autoinc_lock_mode=2
innodb_flush_log_at_trx_commit=0
innodb_buffer_pool_size=122M

wsrep_provider=/usr/lib64/galera-3/libgalera_smm.so
wsrep_provider_options="gcache.size=300M; gcache.page_size=300M"
; 这个名字在集群中必须是统一的
wsrep_cluster_name="my-cluster"
; 集群中其他IP，用逗号分隔，自己的IP也要加
wsrep_cluster_address="gcomm://IP1,IP2,IP3"
wsrep_sst_method=rsync
; 本机IP
wsrep_node_address="192.168.1.10"
; 集群监听的IP和端口，设置本机IP和默认4567即可
wsrep_provider_options ="gmcast.listen_addr=tcp://192.168.1.10:4567"
; 节点名称，集群里唯一
wsrep_node_name=db10
; 同步用的账号密码
wsrep_sst_auth=root:password
```



