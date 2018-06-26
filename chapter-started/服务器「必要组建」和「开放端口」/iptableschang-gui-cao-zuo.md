# iptables的端口控制(非必须)

比如服务器已关闭iptables(阿里云)，则无需配置下列项

## 开放必要端口
```
# 开放22(SSH)、80(Web)、3360（MySQL）、11211(Memcache)
$ iptables -A INPUT -p tcp -m multiport --port 22,80,3360,11211

# 开放本地回路
$ iptables -A INPUT -s 127.0.0.1 -d 127.0.0.1 -j ACCEPT

# 保存并重启iptables
$ service iptables save
$ service iptables restart
```

## 进阶：建立一个只开放22、80端口的iptables
```
# 取消其他端口的访问规则
$ iptables -P INPUT DROP
$ iptables -P FORWARD DROP
$ iptables -P OUTPUT DROP

# 开放22、80
$ iptables -A INPUT -p tcp --dport 22 -j ACCEPT
$ iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
$ iptables -A INPUT -p tcp --dport 80 -j ACCEPT
$ iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
$ iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT # 允许已建立的或相关连的通行
$ iptables -A OUTPUT -j ACCEPT # 允许所有本机向外的访问

# 保存并重启iptables
$ service iptables save
$ service iptables restart
```

