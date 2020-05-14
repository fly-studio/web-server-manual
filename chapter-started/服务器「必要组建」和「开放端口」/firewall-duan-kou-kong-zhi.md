# # CentOS 7.0 firewall 端口控制
> https://access.redhat.com/documentation/zh-CN/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html

## ## 安装firewall

CentOS 7 自带，无需单独安装

```
$ yum install firewalld firewall-config
```

## ## 操作

启动/停止/重启/开机启动/取消自启：

```
$ systemctl start/stop/restart/enable/disable firewalld
```

查看状态：

```
$ systemctl status firewalld 或者 firewall-cmd --state
```


查看版本

```
$ firewall-cmd --version
```
查看所有版本
```
$ firewall-cmd --list-all
```

## ## Zone的概念

#### drop（丢弃）
任何接收的网络数据包都被丢弃，没有任何回复。仅能有发送出去的网络连接。
#### block（限制）
任何接收的网络连接都被 IPv4 的 icmp-host-prohibited 信息和 IPv6 的 icmp6-adm-prohibited 信息所拒绝。
#### public（公共） 默认的
在公共区域内使用，不能相信网络内的其他计算机不会对您的计算机造成危害，只能接收经过选取的连接。
#### external（外部）
特别是为路由器启用了伪装功能的外部网。您不能信任来自网络的其他计算，不能相信它们不会对您的计算机造成危害，只能接收经过选择的连接。
#### dmz（非军事区）
用于您的非军事区内的电脑，此区域内可公开访问，可以有限地进入您的内部网络，仅仅接收经过选择的连接。
#### work（工作）
用于工作区。您可以基本相信网络内的其他电脑不会危害您的电脑。仅仅接收经过选择的连接。
#### home（家庭）
用于家庭网络。您可以基本信任网络内的其他计算机不会危害您的计算机。仅仅接收经过选择的连接。
#### internal（内部）
用于内部网络。您可以基本上信任网络内的其他计算机不会威胁您的计算机。仅仅接受经过选择的连接。
#### trusted（信任）
可接受所有的网络连接。

## ## 操作Zone

查看正在活动的区域: 
```
$ firewall-cmd --get-active-zones
```
获取默认区域
```
$ firewall-cmd --get-default-zone
```
设置默认接口区域(无需重启立即生效)
```
$ firewall-cmd --set-default-zone=public
```
查看指定接口所属区域：
```
$ firewall-cmd --get-zone-of-interface=eth0
```
将接口添加到区域(默认接口都在public)
```
# 临时
$ firewall-cmd --zone=public --add-interface=eth0
# 永久
$ firewall-cmd --zone=public --add-interface=eth0 --permanent
```
移除接口
```
$ firewall-cmd --remove-interface=eth0
```
## ##　操作端口
查看dmz所有打开的端口：
```
$ firewall-cmd --zone=dmz --list-ports
```
加入一个端口到dmz区域：
```
# 临时
$ firewall-cmd --zone=dmz --add-port=8080/tcp
# 永久
$ firewall-cmd --zone=dmz --add-port=8080/tcp --permanent
# 查询该端口是否成功
$ firewall-cmd --zone=dmz --query-port=8080/tcp
```
## ## 操作服务
> /etc/firewalld 目录下有services文件夹

```
$ firewall-cmd --zone=work --add-service=smtp
# 移除服务
$ firewall-cmd --zone=work --remove-service=smtp
```

## ## 更新防火墙规则：
> 两者的区别就是第一个无需断开连接，就是firewalld特性之一动态添加规则，第二个需要断开连接，类似重启服务

```
$ firewall-cmd --reload
$ firewall-cmd --complete-reload
```

## ## 拒绝
拒绝所有包：
```
$ firewall-cmd --panic-on
```
取消拒绝状态：
```
$ firewall-cmd --panic-off
```
查看是否拒绝：
```
$ firewall-cmd --query-panic
```

## ## 开放80端口HTTP服务
```
$ firewall-cmd --zone=public --add-port=80/tcp --permanent
$ firewall-cmd --zone=public --add-service=http --permanent
```
查询是否正常
```
$ firewall-cmd --zone=public --list-ports
$ firewall-cmd --zone=public --list-services
```