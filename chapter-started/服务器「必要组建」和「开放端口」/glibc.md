## ## GLIBC

自行选择是否编译安装，MySQL 5.7 需要glibc 2.17 +

不建议自行安装，如果没有一定动手能力，会严重导致系统命令无法访问。


> 注意：CentOS 6 下很难编译通过，如果要使用MySQL的最新版，建议使用MySQL的仓库`yum install`

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

