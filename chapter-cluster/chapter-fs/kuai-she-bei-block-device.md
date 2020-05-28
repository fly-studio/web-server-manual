# 块设备 Block Device

初始化osd池

```
$ ceph osd pool create rbd 8
$ rbd pool init rbd
```

> 关联rbd的默认pool
> 
>` ceph osd pool application enable your_rbd rbd`

创建一个块镜像

```
$ rbd create --size 1024 foo
```

## 映射到其他机器(需先安装ceph)

将某块映射到本地磁盘

```
$ rbd map rbd/foo
```

会提示操作系统上不支持一些特性，按照需要屏蔽这些

```
$ rbd feature disable foo object-map fast-diff deep-flatten
```

或者在所有`node`下加`/etc/ceph/ceph.conf`之后重启

```
[global]
rbd_default_features = 1
```

## 扩容

```
$ rbd resize --size 2G foo
```

目标机器上扩容

```
$ xfs_growfs /ceph/rbd
```

## 快照

创造/删除

> 本质上是完全复制一份并存储在osd中

```
$ rbd snap create rbd/foo@snap123
$ rbd snap rm rbd/foo@snap123
```

查看所有创造的快照

```
$ rbd snap ls rbd/foo
```

回滚，会完全覆盖当前块镜像

> 将快照的文件全部恢复，即先清空块镜像，然后完整复制一遍

```
$ rbd snap rollback rbd/foo@snap123
```

清除所有

```
$ rbd snap purge rbd/foo
```

## 快照Clone到其它块镜像

先保护快照

```
$ rbd snap protect rbd/foo@snap123
```

复制到指定的块镜像中(bar必须不存在)

```
$ rbd clone rbd/foo@snap123 rbd/bar
```

查看所有关联的块镜像，比如上面的rbd/bar

```
$ rbd children rbd/foo@snap123 
```

取消rbd/bar与该快照的关联

> 取消时，会将快照文件全部拷贝到该块镜像（之前只是做引用）

```
$ rbd flatten rbd/bar
```

删除这个快照

> 这个快照在有引用的情况下，比如上面的bar，那么肯定是无法删除的，需要将所有的关联取消（如果关联多，这会耗费非常多的时间）

```
$ rbd snap unprotect rbd/foo@snap123
$ rbd snap rm rbd/foo@snap123
```

## 挂载Clone的快照的问题

Clone的子块镜像和父镜像，在`rbd map`后具有相同的`UUID`，无法在同一台机器上挂载，需要先`umount`一个

通过这个指令可以查看到他们具备相同的`UUID`

```
$ lsblk -f
```