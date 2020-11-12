# 什么是macvlan/ipvlan

这是一种可以让Docker的容器使用宿主机局域网的网络方法，同样的，可以用于跨docker的网络。

场景使用如下：
1. 使用docker搭建了一个软路由，局域网中的其它机器将网关IP设定为该容器
    作为一个网关（旁路由）要接受所有数据，所以拥有单独的IP，并运行与容器中会非常的方便
2. 分别在多台机器上部署了docker，但里面的容器需要通信。
    使用macvlan/ipvlan会暴露所有端口，
    较安全的做法是：将端口映射到宿主机，然后可以利用宿主的端口进行通信。
    最安全的方法是：使用docker swarm，然后使用overlap网络，这样可以做到没端口暴露。只是这种方法需要docker-compoer支持，配置比较麻烦
    
## macvlan 工作模式图

![](/assets/20180717081048987[1])
    
    
## 创建xxvlan网络

注意：下文的`--subnet=192.168.0.0/24`必须是你局域网的网段，同理网关`--gateway=192.168.0.1`也是真实的路由器网关，`parent=eth0`是你宿主机的真实物理网卡。
通过`ip a`查看

### macvlan
```
$ docker create network -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 -o macvlan_mode=bridge macvlan0
```

此处`-o macvlan_mode=bridge`表示使用bridge模式，这种模式

### ipvlan
```
$ docker create network -d ipvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 -o ipvlan_mode=l2 ipvlan0
```

此处`-o ipvlan_mode=l2`表示`ipvlan`运行在2层，这种情况足够使用，如果要使用`l3`，还需要配置路由，比较麻烦，不推荐



