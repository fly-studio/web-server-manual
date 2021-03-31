# 安装

## 创造用户

```
$ groupadd -r prometheus
$ useradd -r -M -s /sbin/nologin -d /tmp prometheus
```

## 添加目录
```
$ mkdir -p /www/database/prometheus
```

## 下载

```
$ wget https://github.com/prometheus/prometheus/releases/download/v2.25.2/prometheus-2.25.2.linux-amd64.tar.gz
$ tar zxvf prometheus-*.tar.gz
$ cd prometheus-*
```