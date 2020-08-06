# 安装OpenVSwitch

下载

```
$ wget https://www.openvswitch.org/releases/openvswitch-2.13.1.tar.gz
$ tar zxvf openvswitch-*.tar.gz
$ cd openvswitch-*
$ ./boot.sh
$ ./configure --with-linux=/lib/modules/`uname -r`/build
```

查看系统内核版本

```
$ uname -r
$ rpm -qa | grep "kernel-devel"
```