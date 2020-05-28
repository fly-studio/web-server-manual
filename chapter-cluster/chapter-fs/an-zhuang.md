# 安装

本文以安装`luminous`为例，参照：https://docs.ceph.com/docs/luminous/start/

## 结构

- 部署机 x 1 

 下文`ceph-deploy`开头的指令，均指在部署机器上执行

- CEPH Node x 3 (至少)

## 同步时间

在每一台上安装时间同步服务，参照 [时间同步](/chapter-started/shi-jian-tong-bu.md)

## 准备工作

### Host 修改

每一台都需要添加如下hosts，自行修改为对应IP

```
ceph-deploy 10.0.0.2
ceph-node1 10.0.0.11
ceph-node2 10.0.0.12
ceph-node3 10.0.0.13
```

### 修改hostname

每个node都需要修改hostname，比如`ceph-node1`

> hostname 不能有 .
>
> 只能有 _ -

```
$ vim /etc/hostname
```

一定要重启

```
$ init 6
```

### 创建用户

#### 每一个node上创建用户

比如下文都使用的`ceph-node`

```
$ useradd -d /home/ceph-node -m ceph-node
$ passwd ceph-node
```

设置用户无密码登录状态

```
$ echo "ceph-node ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ceph-node
$ sudo chmod 0440 /etc/sudoers.d/ceph-node
```

### 部署机可免密登录到node

在部署机上配置

```
$ ssh-keygen
```

配置免密登录

```
$ ssh-copy-id ceph-node@ceph-node1
$ ssh-copy-id ceph-node@ceph-node2
$ ssh-copy-id ceph-node@ceph-node3
```

设置免密hosts

```
$ vim ~/.ssh/config
```

```
Host ceph-node1
   Hostname ceph-node1
   User ceph-node
Host ceph-node2
   Hostname ceph-node2
   User ceph-node
Host ceph-node3
   Hostname ceph-node3
   User ceph-node
```


## 部署机上安装仓库

```
$ vim /etc/yum.repos.d/ceph.repo
```

```

[ceph]
name=Ceph packages for $basearch
baseurl=http://mirrors.163.com/ceph/rpm-luminous/el7/$basearch
enabled=1
gpgcheck=1
priority=1
type=rpm-md
gpgkey=http://mirrors.163.com/ceph/keys/release.asc

[ceph-noarch]
name=Ceph noarch packages
baseurl=http://mirrors.163.com/ceph/rpm-luminous/el7/noarch
enabled=1
gpgcheck=1
priority=1
type=rpm-md
gpgkey=http://mirrors.163.com/ceph/keys/release.asc

[ceph-source]
name=Ceph source packages
baseurl=http://mirrors.163.com/ceph/rpm-luminous/el7/SRPMS
enabled=0
gpgcheck=1
type=rpm-md
gpgkey=http://mirrors.163.com/ceph/keys/release.asc
priority=1
```

## 创建一个部署文件夹

部署机上执行

```
$ mkdir -p /ceph/cluster
$ cd /ceph/cluster
```

## 安装部署工具

部署机上执行

```
$ yum install yum-plugin-priorities ceph-deploy
```

## 为安装参数导入仓库镜像

这个参数表示安装时，会在node上会使用如下仓库镜像

```
$ export CEPH_DEPLOY_REPO_URL=http://mirrors.163.com/ceph/rpm-luminous/el7
$ export CEPH_DEPLOY_GPG_URL=http://mirrors.163.com/ceph/keys/release.asc
```

## 出错后如何处理

如果安装不顺利，需要从头开始，在部署上执行如下

删除所有节点数据和本地配置文件

```
$ ceph-deploy --username=ceph-node purge ceph-node1 ceph-node2 ceph-node3
$ ceph-deploy --username=ceph-node purgedata ceph-node1 ceph-node2 ceph-node3
$ ceph-deploy --username=ceph-node forgetkeys
$ rm -rf *
```

## Monitor 初始化

```
$ ceph-deploy --username=ceph-node new ceph-node1 ceph-node2 ceph-node3
```

##  安装ceph

安装的版本为 `luminous`

```
$ ceph-deploy --username=ceph-node install --release luminous ceph-node1 ceph-node2 ceph-node3
```

### 管理机安装

如果部署机和管理是同一台

```
$ ceph-deploy --release luminous localhost
```

其它服务器可修改localhost为对应的ip

## 防火墙

每台node均执行

```
$ firewall-cmd --zone=public --add-service=ceph-mon --permanent
$ firewall-cmd --zone=public --add-service=ceph --permanent
$ firewall-cmd --reload
$ setenforce 0
```

## 安装Monitor

```
$ ceph-deploy --username=ceph-node mon create-initial
```

## 推送配置文件、密钥到各个node

```
$ ceph-deploy --username=ceph-node admin ceph-node1 ceph-node2 ceph-node3
```

## 管理员(含http后台)

```
$ ceph-deploy --username=ceph-node mgr create ceph-node1 ceph-node2 ceph-node3
```

## 创建磁盘OSD

> 下文的`/dev/sdb`表示需要设置为`ceph`的盘，需要根据具体的情况修改
>
> 一定要再三确认磁盘的设备路径，一时大意会造成灾难


创建并添加磁盘

```

$ ceph-deploy --username=ceph-node osd create ceph-node1 --data /dev/sdb
$ ceph-deploy --username=ceph-node osd create ceph-node2 --data /dev/sdb
$ ceph-deploy --username=ceph-node osd create ceph-node3 --data /dev/sdb
 ```
 
如果node上的这个磁盘有数据，下面的指令可以抹去这个磁盘信息

```
$ wipefs -a /dev/sdb
$ ceph-volume lvm zap /dev/sdb
```

## Meta 源数据

```
$ ceph-deploy --username=ceph-node mds create ceph-node1 ceph-node2 ceph-node3
```

## 网关(对外接口)

```
$ ceph-deploy --username=ceph-node rgw create ceph-node1 ceph-node2 ceph-node3
```