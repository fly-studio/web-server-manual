# # GLIBC

`MySQL 5.7` 需要`glibc 2.17 +` 以及 `libstdc++ 3.4.15 +`

> 注意：以下是`CentOS 6.x` 安装方法，但是即使安装了完毕，`MySQL 5.7` 仍然提示需要`libstdc++.so.6(GLIBCXX_3.4.15)(64bit)` 
> 所以不建议在`CentOS 6.5`下安装`MySQL 5.7`

> `CentOS 7`下默认就是`glibc 2.17` 无需更新

分别运行下面的两个命令可以查看系统的版本
```
$ rpm -qa | grep glibc
$ strings /lib64/libc.so.6 | grep GLIBC
$ strings /usr/lib64/libstdc++.so.6 | grep GLIBC
```

## Yum 方式安装(推荐)

```
$ wget http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-2.17-55.el6.x86_64.rpm \
http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-common-2.17-55.el6.x86_64.rpm \
http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-devel-2.17-55.el6.x86_64.rpm \
http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-headers-2.17-55.el6.x86_64.rpm \
http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/nscd-2.17-55.el6.x86_64.rpm

$ rpm -Uvh glibc-2.17-55.el6.x86_64.rpm \
glibc-common-2.17-55.el6.x86_64.rpm \
glibc-devel-2.17-55.el6.x86_64.rpm \
glibc-headers-2.17-55.el6.x86_64.rpm \
nscd-2.17-55.el6.x86_64.rpm

# 更新libstdc++
$ yum install http://centos.biz.net.id/7/os/x86_64/Packages/compat-libstdc++-33-3.2.3-72.el7.x86_64.rpm
```
> 以上安装之后还是无法安装MySQL 5.7 因为 MySQL 还依赖 glibcxx-3.4.15

## 编译安装

谨慎选择是否编译安装
如果没有一定动手能力，会严重导致系统命令无法访问。

> 注意：CentOS 6 下很难编译通过

下载
```
$ wget http://ftp.gnu.org/gnu/glibc/glibc-2.28.tar.gz
$ tar -zxvf glibc*.tar.gz
$ cd glibc*
$ mkdir build
$ cd build
$ ../configure --prefix=/usr/local/glibc
$ make -j4 & make install
```
设置
```
$ rm -rf /lib64/libc.so.6
$ ln -s /usr/local/glibc/lib/libc-2.27.so /lib64/libc.so.6
$ export LD_LIBRARY_PATH=/usr/local/glibc/lib:${LD_LIBRARY_PATH} 
```

删除之后应该访问不了系统命令

```
$ LD_PRELOAD=/usr/local/glibc/lib/libc-2.27.so  ln -s /usr/local/glibc/lib/libc-2.27.so /lib64/libc.so.6
```

还是访问不了，就用还原了
```
$ LD_PRELOAD=/lib64/libc-2.12.so  ln -s /lib64/libc-2.12.so /lib64/libc.so.6
$ unset LD_LIBRARY_PATH
```

## ## 问题

### ### 如何解决类似 /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found 的报错

1. 查看GLBCXX版本
```
$ strings /usr/lib64/libstdc++.so.6 | grep GLIBCXX
```

笔者在`CentOS 6.X` 下得到的是`GLIBCXX_3.4.13` 版本有点低

2. 查看libstdc++.so.6链接的库
```
$ ll /usr/lib64/libstdc++.so.6
```

得到结果为版本比较低
```
$ /usr/lib64/libstdc++.so.6 -> libstdc++.so.6.0.13
```

3. 查看系统更高版本的lib库
```
$ find / -name "libstdc++.so.6*"
```
库中没有更高的版本

笔者在CentOS 6.x下做了多种尝试，建议升级系统，别无它法。