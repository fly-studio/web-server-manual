# 时间同步

## 安装

```
# CentOS
$ yum install -y chrony

# Ubuntu/Debian
$ apt install chrony
```

## 修改为阿里云时间服务器

CentOS
```
$ sed -i 's/server 0.centos.pool.ntp.org iburst/server ntp.aliyun.com iburst/g' /etc/chrony.conf
$ sed -i '/^server 1.centos.pool.ntp.org iburst/d' /etc/chrony.conf
$ sed -i '/^server 2.centos.pool.ntp.org iburst/d' /etc/chrony.conf
$ sed -i '/^server 3.centos.pool.ntp.org iburst/d' /etc/chrony.conf
$ sed -i 's/^#allow 192.168.0.0\/16/allow 10.0.7.0\/24/g' /etc/chrony.conf
```

Ubuntu/Debian

$ vim /etc/chrony/chrony.conf
```

选择下面任意一条，注意：配置文件中只能有1条pool
```
pool 2.debian.pool.ntp.org iburst
pool 0.pool.ntp.org iburst
pool ntp.aliyun.com iburst
```

## 启动

```
# CentOS
$ systemctl enable chronyd.service
$ systemctl restart chronyd.service

# Ubuntu/Debian
$ systemctl enable chrony
$ systemctl restart chrony
```

## 查看同步状态

```
$ chronyc activity
```

```
200 OK
0 sources online
0 sources offline
1 sources doing burst (return to online)
0 sources doing burst (return to offline)
0 sources with unknown address
```

```
$ chronyc sourcestats -v
```

可以看到下面有一个同步的IP

```
210 Number of sources = 1
                             .- Number of sample points in measurement set.
                            /    .- Number of residual runs with same sign.
                           |    /    .- Length of measurement set (time).
                           |   |    /      .- Est. clock freq error (ppm).
                           |   |   |      /           .- Est. error in freq.
                           |   |   |     |           /         .- Est. offset.
                           |   |   |     |          |          |   On the -.
                           |   |   |     |          |          |   samples. \
                           |   |   |     |          |          |             |
Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
==============================================================================
203.107.6.88               17   9   847     -0.076      7.905  -3424ns  2119us
```

```

```