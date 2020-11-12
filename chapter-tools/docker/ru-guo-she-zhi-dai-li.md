# 设置代理

百度上的国内镜像大部分已经无法使用

目前只能使用代理来加速：https://docs.docker.com/config/daemon/systemd/#httphttps-proxy


```
$ mkdir -p /etc/systemd/system/docker.service.d
```

此处假设你已经搭建了sock5代理: `127.0.0.1:1080`

## http 代理

```
$ vim /etc/systemd/system/docker.service.d/http-proxy.conf
```

```
[Service]
Environment="HTTP_PROXY=socks5://127.0.0.1:1080" "NO_PROXY=localhost,127.0.0.0/8,registryserver,10.0.0.0/8,192.168.0.0/16"
```

### https 代理

```
$ vim /etc/systemd/system/docker.service.d/https-proxy.conf
```

```
[Service]
Environment="HTTPS_PROXY=socks5://127.0.0.1:1080" "NO_PROXY=localhost,127.0.0.0/8,registryserver,10.0.0.0/8,192.168.0.0/16"
```

## 查看设置的效果

```
$ systemctl daemon-reload
$ systemctl show --property Environment docker
```

重启docker即可生效