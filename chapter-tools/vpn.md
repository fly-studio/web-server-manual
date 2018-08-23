# 安装VPN

```
$ yum -y install ppp pptpd
```
# 配置

## 开启ipv4转发
```
# CentOS 6
$ vim /etc/sysctl.conf

# CentOS 7
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


```
$ vim /etc/ppp/options.pptpd
```

修改DNS
```
ms-dns 223.5.5.5
ms-dns 8.8.8.8
```