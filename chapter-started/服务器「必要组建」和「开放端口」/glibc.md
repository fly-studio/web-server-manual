# # GLIBC

MySQL 5.7 需要glibc 2.17 +
以下是CentOS 6.x 安装方法，


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
```

## 编译安装

谨慎选择是否编译安装
如果没有一定动手能力，会严重导致系统命令无法访问。


> 注意：CentOS 6 下很难编译通过

分别运行下面的两个命令可以查看系统的版本
```
$ rpm -qa | grep glibc
$ strings /lib64/libc.so.6 | grep GLIBC
```
下载
```
$ wget http://ftp.gnu.org/gnu/glibc/glibc-2.27.tar.gz
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

