# 安装VPN

```
$ yum -y install ppp pptpd
```

# 配置

## 开启ipv4转发

```
# 6.x
$ vim /etc/sysctl.conf

# 7.x
$ vim /usr/lib/sysctl.d/50-default.conf
```

添加或修改如下内容

```
net.ipv4.ip_forward = 1
```

生效

```
$ sysctl -p
```

## DNS 配置

```
$ vim /etc/ppp/options.pptpd
```

添加或修改DNS，DNS服务器自定

```
ms-dns 223.5.5.5
ms-dns 8.8.8.8
```

## VPN IP 端

```
$ vim /etc/pptpd.conf
```

修改如下内容

> 注意: 这里的IP都是虚拟的，是指客户端拨号上来之后的分配的网关、IP

```
localip 192.168.0.1 # 虚拟的网关地址
remoteip 192.168.0.2-254 # 客户端动态分配的IP
```

> 这里有个坑，此配置文件最后一行必须为空行，原因目前未知

# 用户

用户文件在 `/etc/ppp/chap-secrets`

```
#client server secret IP-addresses

username pptpd password *
```

# 转发

## iptable

CentOS中使用如下配置

> 此处192.168.0.0就是上面虚拟的IP段

```
$ iptables -A INPUT -p tcp -m tcp --dport 1723 -j ACCEPT
$ iptables -A FORWARD -i ppp+ -j ACCEPT
$ iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
$ iptables -A FORWARD -p tcp -syn -s 192.168.0.0/24 -j TCPMSS -set-mss 1496
```

保存

```
$ service iptables save
```

## firewall

```
$ firewall-cmd --zone=public --add-interface=ppp+ --permanent
$ firewall-cmd --permanent --new-service=pptp
$ cat >/etc/firewalld/services/pptp.xml<<EOF
<?xml version="1.0" encoding="utf-8"?>
<service>
  <port protocol="tcp" port="1723"/>
</service>
EOF

$ firewall-cmd --permanent --zone=public --add-service=pptp
$ firewall-cmd --permanent --zone=public --add-masquerade

$ firewall-cmd --zone=public --permanent --direct --add-rule ipv4 filter INPUT 0 -i eth0 -p tcp --dport 1723 -j ACCEPT 
$ firewall-cmd --zone=public --permanent --direct --add-rule ipv4 filter INPUT 0 -p gre -j ACCEPT 
$ firewall-cmd --zone=public --permanent --direct --add-rule ipv4 filter POSTROUTING 0 -t nat -o eth0 -j MASQUERADE 
$ firewall-cmd --zone=public --permanent --direct --add-rule ipv4 filter FORWARD 0 -i ppp+ -o eth0 -j ACCEPT 
$ firewall-cmd --zone=public --permanent --direct --add-rule ipv4 filter FORWARD 0 -i eth0 -o ppp+ -j ACCEPT
$ firewall-cmd --zone=public --permanent --direct --add-rule ipv4 filter FORWARD 0 -p tcp -i ppp+ -j TCPMSS --syn --set-mss 1496
```

生效

```
$ firewall-cmd --reload
```


# 问题

## 无法访问百度，亚马逊等，但是其它网站正常

这是因为MTU太大导致包被丢弃的问题

在 `/etc/ppp/options.pptpd`加入这个并重启pptpd

```
mtu 1496
```

上面的iptables 和 firewall都有加




