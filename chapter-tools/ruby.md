# # Ruby
## ## RVM Ruby Virtual Manage
必要依赖
```
$ yum install gcc-c++ patch readline readline-devel zlib zlib-devel
$ yum install libyaml-devel libffi-devel openssl-devel make
$ yum install bzip2 autoconf automake libtool bison iconv-devel sqlite-devel
```
rvm
```
$ curl -sSL https://get.rvm.io | bash -s stable
```
刷新系统环境变量
```
$ source /etc/profile.d/rvm.sh
```
安装ruby
```
$ rvm install 2.3.1
```
选择默认版本
```
$ rvm use 2.3.1 --default
```

## ## 编译安装
卸载旧版本
```
$ rpm -e --nodeps ruby
```
安装
```
$ wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz
$ tar zxvf ruby-2.3.1.tar.gz
$ cd ruby-2.3.1
$ ./configure
$ make
$ sudo make install
```
刷新环境变量
```
$ source /etc/profile
```