# # GCC


#### 默认版本

> CentOS 6 中默认的gcc版本为： gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-17)
> CentOS 7.3 中默认的gcc版本为： gcc-v4.8.5 (GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)


#### - 4.8
> 因为CentOS 7 自带仓库的GCC就是4.8，需要升级可以选择安装 4.9

安装源
```
$ wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
```
安装
```
$ yum install devtoolset-2-binutils devtoolset-2-gcc devtoolset-2-gcc-c++

# 备份之前的gcc，如果之前没有安装可以跳过
$ mv /usr/bin/gcc /usr/bin/gcc-old
$ mv /usr/bin/g++ /usr/bin/g++-old
$ mv /usr/bin/cc /usr/bin/cc-old

# 将新版的gcc链接到系统目录

$ ln -fs /opt/rh/devtoolset-2/root/usr/bin/gcc /usr/bin/gcc
$ ln -fs /opt/rh/devtoolset-2/root/usr/bin/gcc /usr/bin/cc
$ ln -fs /opt/rh/devtoolset-2/root/usr/bin/g++ /usr/bin/g++
```
#### - 4.9
安装源

如果下面面的包不存在，请先安装[Sclo-RH源](/chapter-started/repo-仓库.md)
```
$ yum install centos-release-scl-rh
```
安装
```
$ yum install devtoolset-3-binutils devtoolset-3-gcc devtoolset-3-gcc-c++

# 备份之前的gcc，如果之前没有安装可以跳过
$ mv /usr/bin/gcc /usr/bin/gcc-old
$ mv /usr/bin/g++ /usr/bin/g++-old
$ mv /usr/bin/cc /usr/bin/cc-old

# 将新版的gcc链接到系统目录
$ ln -fs /opt/rh/devtoolset-3/root/usr/bin/gcc /usr/bin/gcc
$ ln -fs /opt/rh/devtoolset-3/root/usr/bin/gcc /usr/bin/cc
$ ln -fs /opt/rh/devtoolset-3/root/usr/bin/g++ /usr/bin/g++
```

#### - 5.x ~ 7.x

按照上例4.9中的指令, 将 3 替换成 5 / 6 / 7 就行，比如：
```
$ yum install devtoolset-7-binutils devtoolset-7-gcc devtoolset-7-gcc-c++
$ ln -fs /opt/rh/devtoolset-7/root/usr/bin/gcc /usr/bin/gcc
```


