NFS 可以让所有服务共享一个文件夹，这样在任意服务器上面上传的文件都可以共享使用。

NFS安装分为服务端和客户端，一个服务端提供服务，可供N个客户端共同使用。
# # 服务端

假设IP是：192.168.1.100

## ## 安装
```
$ yum install nfs-utils rpcbind
```

## ## 开启服务
```
$ systemctl enable nfs-server
$ systemctl enable rpcbind
```
```
$ sytemctl start nfs-server
$ sytemctl start rpcbind
```

## ## 共享文件
```
$ mkdir -p /www/website/data
$ chown nobody:nobody /www/website/data
```

## ## 配置
```
$ vim /etc/exports
```
写入如下配置
```
/www/website/data/ *(rw,anonuid=99,anongid=99,insecure,sync,no_wdelay)
```
- 共享文件夹是：`/www/website/data/`
- 权限是：`rw`
- 文件用户组为：`nobody` 也就是：`anonuid=99,anongid=99`

## 配置详情

- **rw**：read-write，可读写；
- **ro**：read-only，只读；
- **sync**：文件同时写入硬盘和内存；
- **async**：文件暂存于内存，而不是直接写入内存；
- **no_root_squash**：NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，也拥有root权限。显然开启这项是不安全的。
- **root_squash**：NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，拥有匿名用户权限，通常他将使用nobody或nfsnobody身份；
- **all_squash**：不论NFS客户端连接服务端时使用什么用户，对服务端分享的目录来说都是拥有匿名用户权限；
- **anonuid**：匿名用户的UID值，可以在此处自行设定。
- **anongid**：匿名用户的GID值。

## 其它命令
查看运行状况
```
$ rpcinfo -p
```
哪些文件夹共享
```
$ exportfs
```

# 客户端
## ## 安装
```
$ yum install nfs-utils rpcbind
```

## ## 开启服务
```
$ systemctl enable rpcbind
```
```
$ sytemctl start rpcbind
```

## ## 挂载
```
$ vim /etc/fstab
```
添加
```
# 服务端IP:服务端文件夹             本地路径
192.168.1.100:/www/website/data   /client/path/       nfs     defaults        0       0
```
挂载
```
$ mount -a
```

## 注意

- 如果服务端修改了配置，客户端需要重启 `rpcbind`

