# YUM 安装

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



