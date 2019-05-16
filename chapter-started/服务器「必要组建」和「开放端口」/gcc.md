# # GCC

## 默认版本

> CentOS 6 中默认的gcc版本为： gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-17)
> CentOS 7.3 中默认的gcc版本为： gcc-v4.8.5 (GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)

## 查看现在版本

```
$ gcc --version
```

## - 4.8 
> 注意：CentOS 7 自带仓库的GCC就是4.8

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
## - 4.9 ~ 7.x
安装源，如果下面面的包不存在，请先安装[Sclo-RH源](/chapter-started/repo-仓库.md)
```
$ yum install centos-release-scl-rh
```
安装 4.9
```
$ yum install devtoolset-3-binutils devtoolset-3-gcc devtoolset-3-gcc-c++
```
安装 5.x
```
$ yum install devtoolset-5-binutils devtoolset-5-gcc devtoolset-5-gcc-c++
```
安装 6.x
```
$ yum install devtoolset-6-binutils devtoolset-6-gcc devtoolset-6-gcc-c++
```
安装 7.x
```
$ yum install devtoolset-7-binutils devtoolset-7-gcc devtoolset-7-gcc-c++
```

### 安装之后

```
# 备份之前的gcc，如果之前没有安装可以跳过
$ mv /usr/bin/gcc /usr/bin/gcc-old
$ mv /usr/bin/g++ /usr/bin/g++-old
$ mv /usr/bin/cc /usr/bin/cc-old

# 将新版的gcc链接到系统目录，
# 注意，下面的 3 请替换成上面安装的对应版本
$ ln -fs /opt/rh/devtoolset-3/root/usr/bin/gcc /usr/bin/gcc
$ ln -fs /opt/rh/devtoolset-3/root/usr/bin/gcc /usr/bin/cc
$ ln -fs /opt/rh/devtoolset-3/root/usr/bin/g++ /usr/bin/g++
```

