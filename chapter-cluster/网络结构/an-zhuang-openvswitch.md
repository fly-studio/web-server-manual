# 安装OpenVSwitch

预环境

```
$ yum -y install openssl-devel wget kernel-devel
$ yum groupinstall "Development Tools"
```

下载

```
$ wget https://www.openvswitch.org/releases/openvswitch-2.13.1.tar.gz
$ tar zxvf openvswitch-*.tar.gz
$ cd openvswitch-*
$ ./boot.sh
$ ./configure --prefix=/usr --with-linux=/lib/modules/`uname -r`/build
```

查看系统内核版本

```
$ uname -r
$ rpm -qa | grep "kernel-devel"
```