# 文件系统 Filesystem

先创建`cephfs_data`，`cephfs_metadata`

再将这两个指定到新的`cephfs`

```
ceph osd pool create cephfs_data 8
ceph osd pool create cephfs_metadata 8
ceph fs new cephfs cephfs_metadata cephfs_data
```

> 关联cephfs的默认pool
> 
>` ceph osd pool application enable your_cephfs cephfs`

## 映射其他机器(需先安装ceph)


将`/ceph/cluster/ceph.client.admin.keyring`中的Base64密码复制出来，
写到`/ceph/cluster/admin.secret`


```
mount -t ceph ceph-node1:6789:/ /ceph/data/ -o name=admin,secretfile=/ceph/cluster/admin.secret
```

## insufficient standby MDS daemons available
`MDS` 没有 `standby` 的

将活动的mds变成待机

```
ceph fs set cephfs max_mds 数量
ceph mds deactivate cephfs:数量

```