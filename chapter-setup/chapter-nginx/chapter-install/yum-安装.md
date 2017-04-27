# # yum安装方式
> 如果需要支持HTTPS，请选择编译安装

配置官方源
```
$ vim /etc/yum.repos.d/nginx.repo
```
内容
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```
安装
```
$ yum install nginx
```

