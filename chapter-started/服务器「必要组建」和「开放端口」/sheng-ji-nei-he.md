# 升级Linux内核

## 查看内核版本

```
$ uname -r
```

或 
```
$ rpm -qa | grep kernel
```

`CentOS 7`一般是`3.10.xxx`

## 升级

### 1. 安装仓库

```
$ rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
$ yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
```

### 2. 查看可用的内核

```
$ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
```

- `kernel-lt` 是 `4.x` 版本
- `kernel-ml` 是 `5.x` 版本

### 3. 安装

这里安装 4.x的版本即可

```
$ yum --enablerepo=elrepo-kernel install kernel-lt
```

### 4. 设置Grub2

内核安装好后，需要设置为默认启动选项并重启后才会生效

#### 查看系统上所有的内核

```
$ awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
```

会得到一个列表

```
0: CentOS Linux (4.x) 7 (Core)
1: CentOs Linux (3.x) 7 (Core)
```

#### 设定为最新内核

0 为上面最新的内核序号

```
$ grub2-set-default 0
```

或者编辑 `/etc/default/grub`

```
GRUB_DEFAULT=0
```

#### 生成grub配置并重启

```
$ grub2-mkconfig -o /boot/grub2/grub.cfg
```

然后重启

### 5. 清理旧内核

只有在安装的内核大于 3 个时，以下指令才会自动删除旧内核

```
$ yum install yum-utils
$ package-cleanup --oldkernels
```



