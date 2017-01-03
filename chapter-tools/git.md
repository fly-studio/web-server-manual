# # Git

安装最新版本的git-core
## ## yum 安装
```
$ yum install http://opensource.wandisco.com/centos/6/git/x86_64/wandisco-git-release-6-1.noarch.rpm
```
```
$ yum install git
```
## ## 源代码编译
依赖安装
```
$ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
$ yum install  gcc perl-ExtUtils-MakeMaker
```
卸载旧版本
```
yum remove git
```
下载/编译
```
# 下载解压
$ cd /usr/src
$ wget https://www.kernel.org/pub/software/scm/git/git-2.10.1.tar.gz
$ tar -zxvf git-2.10.1.tar.gz

# 编译
$ cd git-2.10.1
$ make prefix=/usr/local/git all
$ make prefix=/usr/local/git install
#
$ echo 'export PATH=$PATH:/usr/local/git/bin' >> /etc/bashrc
#  or
$ echo 'export PATH=$PATH:/usr/local/git/bin' > /etc/profile.d/git.sh
#
$ source /etc/bashrc
```
> Updated method of adding compiled git bin directory to bashrc. Because echo "export PATH=$PATH:/usr/local/git/bin" >> /etc/bashrc used "" instead of '', it would expand the current session's value for $PATH instead of keeping it as a variable, and could adversely affect the entire system. At the minimum, it should use '' instead of "" and should really be a separate script in /etc/profile.d/

## ## 查看版本
```
$ git --version
```