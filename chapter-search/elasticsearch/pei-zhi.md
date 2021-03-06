# # 配置

## 设置运行目录
```
$ mkdir /www/database/elasticsearch
$ chown -R elasticsearch:elasticsearch /www/database/elasticsearch
```

## 编辑配置

```
$ vim /etc/elasticsearch/elasticsearch.yml
```
```
cluster.name: 你应用的名字，集群保持一致
# 设置索引数据的存储路径，默认是es根目录下的data文件夹
path.data: /www/database/elasticsearch
# 强制所有内存锁定，不要搞什么swap的来影响性能 
# 设置为true来锁住内存。因为当jvm开始swapping时es的效率会降低，所以要保证它不swap，可以把ES_MIN_MEM和ES_MAX_MEM两个环境变量设置成同一个值，并且保证机器有足够的内存分配给es。同时也要允许elasticsearch的进程可以锁住内存，linux下可以通过`ulimit -l unlimited`命令。 
bootstrap.memory_lock: true

# 这个参数是用来同时设置bind_host和publish_host上面两个参数。
# (默认为_local_) _local_ 本地访问  _site_ 局域网访问 _global_都可以访问
network.host: _local_
# 端口
http.port: 9200
# 内存占比
indices.memory.index_buffer_size: 50%
indices.queries.cache.size: 50%
# 可跨域请求
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-methods: OPTIONS, HEAD, GET, POST, PUT, DELETE
http.cors.allow-headers: X-Requested-With, Content-Type, Content-Length, Authorization
```

> bootstrap.memory_lock 开启之后无法启动的问题见



## 其他配置
```
# 设置绑定的ip地址，可以是ipv4或ipv6的，默认为0.0.0.0
network.bind_host: 0.0.0.0
# 设置其它节点和该节点交互的ip地址，如果不设置它会自动判断，值必须是个真实的ip地址。
network.publish_host: 192.168.0.1
# 与其他节点交换数据用的端口
transport.tcp.port: 9300  
```

> 注意：如果服务器有多IP，那么最好指定 `network.publish_host`
> 比如uCloud的负载均衡会让本地多一个lo.1的VIP，ES会优先发布到此VIP上导致无法集群，指定本机IP后可以解决无法启动的问题
