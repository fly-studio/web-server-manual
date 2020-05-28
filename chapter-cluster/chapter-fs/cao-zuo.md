# 操作


## 同步配置

```
ceph-deploy --username=ceph-node --overwrite-conf config push ceph-node1 ceph-node2 ceph-node3
```

## 带用户的命令行

以查看状态为例

### 在任意node上执行

```
ceph -s
ceph quorum_status --format json-pretty
```

### 或在admin上

需要将部署机的ceph.client.admin.keyring拷贝至本地

```
ceph -c /ceph/cluster/ceph.conf -k /ceph/cluster/ceph.client.admin.keyring --id admin status
```

## 设置CEPH_ARGS

设置这个之后，在admin(或部署机)上执行`ceph、rdb、rados`等指令无需指定用户配置

```
export CEPH_ARGS="-c /ceph/cluster/ceph.conf -k /ceph/cluster/ceph.client.admin.keyring --id admin"

```