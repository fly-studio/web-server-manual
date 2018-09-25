# 增量备份

> 部分文档参考来源: https://qizhanming.com/blog/2017/05/10/install-percona-xtrabackup-on-centos-7

使用`Percona Xtrabackup`进行备份和增量备份。

## 前提

- MySQL > 5.1.23
- 足够的空间


## 安装

> 参考: https://www.percona.com/doc/percona-xtrabackup/LATEST/installation/yum_repo.html


```
# 安装仓库
$ yum install http://www.percona.com/downloads/percona-release/redhat/0.1-6/percona-release-0.1-6.noarch.rpm
# 安装软件
$ yum install percona-xtrabackup-24
```

## 备份需要的MySQL用户

自行修改下文的用户和密码
```
CREATE USER 'backup'@'localhost' IDENTIFIED BY 'password';
GRANT RELOAD, LOCK TABLES, REPLICATION CLIENT, CREATE TABLESPACE, PROCESS, SUPER, CREATE, INSERT, SELECT ON *.* TO 'backup'@'localhost';
FLUSH PRIVILEGES;
```

## 创建配置文件

比如配置的路径为 `/data/backup.conf`
内容为上文设置的用户和密码:

```
[client]
user=backup
password=password
```


## 备份脚本

### 原理解释

本脚本第一次运行会生成一个全备份，`lsn_path`文件中的格式类似于
```
backup_type = full-backuped
from_lsn = 0
```

之后运行时，则是增量备份，内容类似
```
backup_type = incremental
```

每次备份都会以当前时间为文件夹

### 脚本内容

比如文件名：
```
$ vim xtrabackup.sh
```

#### 变量解释

- base_path：备份目录
- config_path：配置文件路径，参照上文
- log_file：日志文件路径
- lsn_path：最后一次成功备份的数据起始点文件，（无需修改）
- processors：CPU核数（无需修改）

```bash
#!/bin/bash

base_path="/data/mysql_backup/"
config_path="/data/backup.conf"
log_file="/data/backup.log"

lsn_path="${base_path}/xtrabackup_checkpoints"
processors="$(nproc --all)"

innobackupex_args=(
	"--defaults-file=${config_path}"
	"--extra-lsndir=${base_path}"
	"--parallel=${processors}"
	"--compress"
	"--compress-threads=${processors}"
	"--slave-info"
	"--use-memory=2G"
	"--incremental"
)

if grep -q -s "to_lsn" "${lsn_path}"; then
    lsn=$(awk '/to_lsn/ {print $3;}' "${lsn_path}")
    innobackupex_args+=( "--incremental-lsn=${lsn}" )
fi

innobackupex "${innobackupex_args[@]}" "${base_path}" 2>> "${log_file}"

# Check success and print message
if tail -1 "${log_file}" | grep -q "completed OK"; then
    printf "Backup successful!\n"
else
    error "Backup failure! Check ${log_file} for more information"
fi
```

