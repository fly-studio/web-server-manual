# Yum 安装

## ## 安装 Erlang


### ### 安装YUM库


#### #### 一键安装

```
$ wget https://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
$ rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
```

#### #### 手动添加仓库

```
$ vim /etc/yum.repo.d/erlang.repo
```

```
[erlang-solutions]
name=CentOS $releasever - $basearch - Erlang Solutions
baseurl=https://packages.erlang-solutions.com/rpm/centos/$releasever/$basearch
gpgcheck=1
gpgkey=https://packages.erlang-solutions.com/rpm/erlang_solutions.asc
enabled=1
```

导入KEY

```
$ rpm --import https://packages.erlang-solutions.com/rpm/erlang_solutions.asc
```

## 安装

```
$ yum install erlang
```

## 安装RabbitMQ

去`https://github.com/rabbitmq/rabbitmq-server/releases`找到最新版的rpm包
比如CentOS 7是el7.noarch.rpm

```
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.4/rabbitmq-server-3.8.4-1.el7.noarch.rpm
```

安装

```
$ yum install rabbitmq-server-*.noarch.rpm
```

