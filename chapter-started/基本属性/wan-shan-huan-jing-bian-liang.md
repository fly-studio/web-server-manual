本文主要是将/usr/local/加入系统环境变量


## 导入lib库到全局
```
$ vim /etc/ld.so.conf
```
追加
```
/usr/local/lib
/usr/local/lib64
```
执行导入
```
$ ldconfig -v
```

## 完善环境变量
```
$ visudo
```

在```Defaults    secure_path```后添加
```
:/usr/local/bin
```

刷新
```
$ source /etc/profile
```
## 本地配置文件

```
$ vim ~/.bash_profile
```

添加如下语句，以确保环境变量正常
```
alias sudo='sudo env PATH=$PATH'
```

刷新
```
source ~/.bash_profile
```
