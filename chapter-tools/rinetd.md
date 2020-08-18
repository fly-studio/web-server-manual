# rinet 

Rinetd是为在一个Unix和Linux操作系统中为重定向传输控制协议(TCP)连接的一个工具。Rinetd是单一过程的服务器，尽管rinetd使用非闭锁I/O运行作为一个单一过程，它可能重定向很多连接而不对这台机器增加额外的负担。

> 生产环境中谨慎使用rinetd


## 源代码方式安装

```
$ git clone https://github.com/boutell/rinetd.git
$ cd rinetd
$ mkdir -p /usr/man/man8
$ make && make install
```

## RPM方式安装
```
# CentOS 7
$ wget http://li.nux.ro/download/nux/misc/el7/x86_64/rinetd-0.62-9.el7.nux.x86_64.rpm
$ rpm  -ivh  rinetd-0.62-9.el7.nux.x86_64.rpm
```


## 配置

新建配置文件
```
$ vim /etc/rinetd.conf
```

正文内容

```
# 绑定IP 绑定Port 目标地址 目标Port
0.0.0.0 221 10.0.0.2 22

# 源IP 源Port 目标地址 目标Port
1.2.3.4 80 10.0.0.3 80

# 允许连接IP
allow *.*.*.*
# 拒绝连接IP
deny 1.2.3.*
logfile /var/log/rinetd.log
```

> 一个配置一行， 目标地址可以为域名
> 1. 将所有发往本机`221`端口，转发到`10.0.0.2`的`22`端口
> 2. 将所有发往`1.2.3.4`的`80`端口，转发到`10.0.0.3`的`80`端口。通过netstat -tap可以看到
> 3. 允许IP连接到本机为 `*.*.*.*`
> 4. 拒绝IP连接到本机为1.2.3.0/24
> 5. 日志路径

## 注册成服务

```
$ vim  /etc/systemd/system/rinetd.service
```

```
##########################################################

[Unit]

Description=Rinetd Daemon

After=network.service

Wants=network.service

[Service]

Type=forking

PIDFile=/var/run/rinetd.pid

ExecStart=/usr/local/rinetd/sbin/rinetd -c /usr/local/rinetd/etc/rinetd.conf

Restart=on-failure

[Install]

WantedBy=multi-user.target

###############################################################
```