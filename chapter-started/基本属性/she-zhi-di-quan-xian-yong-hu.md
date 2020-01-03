# 添加用户

切换到root下
```
$ useradd new_name
$ passwd new_name
```

设置sudo属性
```
$ visudo
```

在`root    ALL=(ALL)       ALL`下添加
```
new_name	ALL=(ALL)	ALL
```

