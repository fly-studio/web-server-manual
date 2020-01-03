# # Go
## ## yum 安装
天地良心，epel库里golang是最新版
```
yum install go
```

## ## 编译安装
```
$ wget https://storage.googleapis.com/golang/go1.7.1.linux-amd64.tar.gz
$ tar -C /usr/local -xzf go1.7.1.linux-amd64.tar.gz
$ ln -sf /usr/local/go/bin/{go,godoc,gofmt} /usr/local/bin/
```