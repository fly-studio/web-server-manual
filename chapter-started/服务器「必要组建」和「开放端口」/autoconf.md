## ## autoconf
一般情况下用`yum install`安装即可，以下是编译安装最新版 2.69

```
$ wget ftp://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz
$ tar -zxvf autoconf*.tar.gz
$ cd autoconf-*
$ ./configure --prefix=/usr/
$ make && make install
```

查看版本

```
$ autoconf -V
```




