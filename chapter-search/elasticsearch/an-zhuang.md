# # 安装
## ## 导入公钥
```
$ rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
## ## 安装REPO文件
```
$ vim /etc/yum.repos.d/elasticsearch.repo
```
```
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

## ## 安装
安装java(如果有则跳过)
```
$ yum install java java-devel
```
安装
```
$ yum install elasticsearch
```
