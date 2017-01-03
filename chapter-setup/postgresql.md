# 安装
## ## 安装Repo库
> 手动访问 https://yum.postgresql.org/9.6/redhat 替换为最新的地址，结尾：9.6-3.noarch.rpm

```
$ wget http://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-6.5-x86_64/pgdg-centos96-9.6-3.noarch.rpm
$ rpm -ivh pgdg-*.rpm
```
## ## 服务器端
```
$ yum install postgresql96-server postgresql96-contrib
# 默认安装，安装的是较低的版本 
$ yum install postgresql-server
```
## ## php端
```
$ yum install -y postgresql postgresql-client libpq-dev postgresql-contrib
```

# 配置

## ## 新建数据库目录
```
$ mkdir /www/database/pgsql/
$ chown postgres:postgres /www/database/pgsql/
```

## ## 初始化数据库
默认数据库位置
```
/var/lib/pgsql/9.6/data
```
修改目录指向
```
$ service postgresql-9.6 initdb --PGDATA="/www/database/pgsql/"
# 默认安装，下同
$ service postgresql initdb
```

## ## 登录
使用postgres 可以无密码登录psql
```
$ sudo -u postgres psql
```
所以删除密码，修改密码如下：（linux的命令）
```
$ sudo passwd -d postgres
$ sudo -u postgres passwd
```
## ## 开放IP登录
主配置文件
```
$ vim /var/lib/pgsql/9.6/datapostgresql.conf
```
```
listen_addresses = '*'
password_encryption = on
max_connections = 10240
```
认证客户端文件
```
$ vim /var/lib/pgsql/9.6/pg_hba.conf
```
```
# "local" is for Unix domain socket connections only
local    all      all                 ident

# IPv4 local connections:
host     all      all   0.0.0.0/0     md5
 
# IPv6 local connections:
host     all      all   ::1/128       md5
```
用户映射文件
```
$ vim 
/var/lib/pgsql/9.6/pg_ident.conf
```
修改IP用户登录密码
```
ALTER USER postgres WITH PASSWORD '123456';
```


# # 操作
## ## 安装系统服务
```
$ chkconfig postgresql-9.6 on
```
## ## 启动服务
```
$ service postgresql-9.6 start
```
## ## 停止服务
```
$ service postgresql-9.6 stop
```
## ## 查看版本
```
$ psql --version
```