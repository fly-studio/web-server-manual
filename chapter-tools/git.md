# # Git

安装最新版本的git-core
## ## yum 安装

安装仓库

```
$ yum install http://opensource.wandisco.com/centos/6/git/x86_64/wandisco-git-release-6-1.noarch.rpm
```

正常安装

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
$ wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.26.1.tar.gz
$ tar -zxvf git-*.tar.gz

# 编译
$ cd git-*
$ make prefix=/usr/ all
$ make prefix=/usr/ install
```

## ## 查看版本
```
$ git --version
```