# 时间同步

## 安装

```
$ yum install -y chrony
```

## 修改为阿里云时间服务器

```
$ sed -i 's/server 0.centos.pool.ntp.org iburst/server ntp.aliyun.com iburst/g' /etc/chrony.conf
$ sed -i '/^server 1.centos.pool.ntp.org iburst/d' /etc/chrony.conf
$ sed -i '/^server 2.centos.pool.ntp.org iburst/d' /etc/chrony.conf
$ sed -i '/^server 3.centos.pool.ntp.org iburst/d' /etc/chrony.conf
$ sed -i 's/^#allow 192.168.0.0\/16/allow 10.0.7.0\/24/g' /etc/chrony.conf
```

## 启动

```
$ systemctl enable chronyd.service
$ systemctl restart chronyd.service
```

## 查看同步状态

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