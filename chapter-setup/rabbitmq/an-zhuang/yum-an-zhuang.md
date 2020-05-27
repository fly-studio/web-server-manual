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