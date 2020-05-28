# 安装

## 同步时间

在每一台上安装时间同步服务，参照 [时间同步](/chapter-started/shi-jian-tong-bu.md)

## 结构

- 部署机 x 1 

- CEPH Node x 3 (至少)

## 预备工作

下文`ceph-deploy`开头的指令，均指在部署机器上执行

### 部署机上导入镜像

```
export CEPH_DEPLOY_REPO_URL=http://mirrors.163.com/ceph/rpm-luminous/el7
export CEPH_DEPLOY_GPG_URL=http://mirrors.163.com/ceph/keys/release.asc
```

### 创建用户

在每一个Node上创建一个用户

```

```

### 免密登录

```

```

## Monitor 初始化

```
ceph-deploy --username=ceph-node new ceph-node1 ceph-node2 ceph-node3
```

##  安装ceph等

```
ceph-deploy --username=ceph-node install --release luminous ceph-node1 ceph-node2 ceph-node3
```

### 管理机安装

如果部署机和管理是同一台

```
ceph-deploy --release luminous localhost
```

其它服务器可修改localhost为对应的ip

## 防火墙

每台node均执行

```
firewall-cmd --zone=public --add-service=ceph-mon --permanent
firewall-cmd --zone=public --add-service=ceph --permanent
firewall-cmd --reload
setenforce 0
```

## 安装Monitor

```
ceph-deploy --username=ceph-node mon create-initial
```

## 拷贝配置文件、密钥到各个节点

```
ceph-deploy --username=ceph-node admin ceph-node1 ceph-node2 ceph-node3
```

## 管理员(含http)

```
ceph-deploy --username=ceph-node mgr create ceph-node1 ceph-node2 ceph-node3
```

## 创建磁盘OSD


如果磁盘有格式

```
wipefs -a /dev/vdb
ceph-volume lvm zap /dev/vdb
```

创建并添加磁盘

```

ceph-deploy --username=ceph-node osd create ceph-node1 --data /dev/vdb
ceph-deploy --username=ceph-node osd create ceph-node2 --data /dev/vdb
ceph-deploy --username=ceph-node osd create ceph-node3 --data /dev/vdb
 ```

## Meta 源数据

```
ceph-deploy --username=ceph-node mds create ceph-node1 ceph-node2 ceph-node3
```

## 网关(对外接口)

```
ceph-deploy --username=ceph-node rgw create ceph-node1 ceph-node2 ceph-node3
```