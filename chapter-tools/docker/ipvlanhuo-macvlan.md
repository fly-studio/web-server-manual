# 什么是macvlan/ipvlan

这是一种可以让Docker的容器使用宿主机局域网的网络方法，同样的，可以用于多台docker中容器的通信。

假设场景如下：

 - 使用docker的容器搭建了一个软路由，局域网中的其它机器将网关IP设定为该容器的IP

 作为网关（旁路由）需要接受所有数据，所以需要拥有局域网IP而不是端口映射，使用`xxxvlan`部署起来会非常方便

- 在多台机器上部署了`docker`，但里面的容器需要通信。
 - 使用`xxxvlan`模式，最简单（但是会暴露所有端口）
 - 较安全的做法是：将端口映射到宿主机，然后可以利用宿主的端口进行通信。
 - 最安全的方法是：使用`docker swarm`部署多台`docker`的集群环境，然后使用`overlap`网络，这样能做到无端口暴露就能通信。只是这种方法启动镜像需要`docker-compoer`支持，配置比较麻烦
 
## 工作模式

链路详情可以查看: https://blog.csdn.net/dog250/article/details/45788279

### macvlan 工作模式图

详情查看: https://blog.csdn.net/dog250/article/details/81074426

![](/assets/20180717081048987[1])

![](/assets/1003074-20190818212109034-918981291[1].png)

### ipvlan 工作模式图

![](/assets/1003074-20190818212141511-935245092[1].png)

## 创建xxvlan网络

注意：下文的`--subnet=192.168.0.0/24`必须是你局域网的网段，同理网关`--gateway=192.168.0.1`也是真实的路由器网关，`parent=eth0`是你宿主机的真实物理网卡。
通过`ip a`查看

### macvlan
```
$ docker network create -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 -o macvlan_mode=bridge macvlan0
```

此处`-o macvlan_mode=bridge`表示使用`bridge`模式，查看上文的模式图。一般情况`bridge`即可

### ipvlan
```
$ docker network create -d ipvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 -o ipvlan_mode=l2 ipvlan0
```

此处`-o ipvlan_mode=l2`表示`ipvlan`运行在2层，这种情况足够使用，如果要使用`l3`，还需要配置路由，比较麻烦，不推荐

### 创建一个基于macvlan的容器

软路由只能使用`macvlan`，因为`ipvlan`的`mac`地址相同，会导致流量从此容器的宿主机就出去了，而不会通过容器

```
# 拉取镜像
$ docker pull registry.cn-shanghai.aliyuncs.com/suling/openwrt:latest

# 启动镜像
$ docker run -it --restart always --network=macvlan0 --ip=192.168.0.100 --privileged -d --name openwrt registry.cn-shanghai.aliyuncs.com/suling/openwrt /sbin/init
```

上面的`--ip=192.168.0.100`根据情况修改

#### 进入容器修改ip

```
$ docker exec -it openwrt bash
$ vim /etc/config/network

```

找到`config interface 'lan'`这一段，修改相关信息为你设置的ip或网关

```
config interface 'lan'
option type 'bridge'
option ifname 'eth0'
option proto 'static'
option ipaddr '192.168.0.100'
option netmask '255.255.255.0'
option ip6assign '60'
option gateway '192.168.0.1'
option broadcast '192.168.0.255'
option dns '223.5.5.5'

```

重启网络(容器里)

```
$ /etc/init.d/network restart

```

此时你可以在局域网中的其它机器使用浏览器打开这个容器`http://192.168.0.100` 账密: `root password`

## 宿主机和容器不能通信

你会发现使用`ipvlan/macvlan`之后，局域网的其它机器和这个容器都能互通，唯独宿主机和这个容器无法互通，因为这是`ipvlan/macvlan`的安全策略导致的，根据上面的工作模式图也可看出来

此时，可以使用一个曲线的方式让他们互通


### 1. 宿主机上创建一个新的ipvlan

创建并启动ipvlan1

```
$ ip $ ip link add ipvlan1 link eth0 type ipvlan mode l2
$ ip l s ipvlan1 up
$ ip a a 192.168.0.101 dev ipvlan1
```

如果是`macvlan`
命令如下:

```
$ ip link add macvlan1 link eth0 type macvlan mode bridge
$ ip l s macvlan1 up
$ ip a a 192.168.0.101 dev macvlan1
```

### 2. 宿主机上指定路由

比如 `192.168.0.100`是容器中的地址，如果有多个容器，就创建多条。`ipvlan1`按照上面的名称修改

```
$ ip route add 192.168.0.100 dev ipvlan1
```

此时你会发现，已经可以在宿主机上`ping`通容器

这句话的意思是本机如果访问`192.168.0.100`就使用`ipvlan1`网卡出去

其实此时在宿主机上如果指定物理网卡去`ping`容器，仍然是不通的，如下

```
# 容器
ping 192.168.0.101 -I eth0

# ipvlan1的ip
ping 192.168.0.101 -I eth0
```


### 3. 容器连接宿主

直接连接`ipvlan1`的ip: `192.168.0.101`即可

如果需要连接宿主机中特殊IP监听的端口，比如宿主中的`redis`配置是`Listen 宿主ip` 或`Listen 127.0.0.1`，此时容器中仍然无法连接宿主，需要改为`Listen 宿主IP, 192.168.0.4`，或者简单改为`Listen 0.0.0.0`即可
