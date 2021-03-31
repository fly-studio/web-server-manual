# 安装

## 创造用户

```
$ groupadd -r prometheus
$ useradd -r -M -s /sbin/nologin -d /tmp prometheus
```

## 创建数据/配置目录
```
$ mkdir -p /www/database/prometheus
$ chown prometheus:prometheus /www/database/prometheus
$ mkdir -p /etc/prometheus
```

## 下载并安装

```
$ wget https://github.com/prometheus/prometheus/releases/download/v2.25.2/prometheus-2.25.2.linux-amd64.tar.gz
$ tar zxvf prometheus-*.tar.gz
$ cd prometheus-*
$ cp -rf prometheus.yml console_libraries consoles /etc/prometheus
$ cp prometheus promtool /usr/bin/
```

## 创建 systemd service unit 文件

```
$ vim /etc/systemd/system/prometheus.service
```

```
[Unit]
Description=Prometheus
After=network.target

[Service]
Type=simple
Environment="GOMAXPROCS=4"
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/www/database/prometheus \
  --storage.tsdb.retention=30d \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.console.templates=/etc/prometheus/consoles \
  --web.listen-address=0.0.0.0:9090 \
  --web.external-url=/prometheus
PrivateTmp=true
PrivateDevices=true
ProtectHome=true
NoNewPrivileges=true
LimitNOFILE=infinity
ReadWriteDirectories=/www/database/prometheus
ProtectSystem=full

SyslogIdentifier=prometheus
Restart=always

[Install]
WantedBy=multi-user.target
```