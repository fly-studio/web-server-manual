# 安装扩展包
```
yum install epel-release
```

# 软件仓库
手册中所有软件仓库都基于以下仓库

## I 安装epel remi软件仓库
```
$ wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
# 7.0
$ wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm

$ wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
# 7.0
$ wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

$ rpm -ivh epel-release-*.rpm
$ rpm -Uvh remi-release-*.rpm
```
remi 软件库包含 PHP 5.4、PHP 5.5、PHP 5.6，以及其对应的扩展
并且默认情况下 remi 库是未激活状态

## II 安装rpmforge仓库
##### 1. 安装

```
$ wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm
# 7.0
$ wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm
$ rpm -ivh rpmforge-release-*.rpm
```

实在不行，用清华大学镜像
> https://mirror.tuna.tsinghua.edu.cn/help/repoforge/

```
$ cat > /etc/yum.repos.d/rpmforge.repo << EOF
[rpmforge]
name = RHEL $releasever - RPMforge.net - dag
baseurl = https://mirrors.tuna.tsinghua.edu.cn/repoforge/redhat/el6/en/$basearch/rpmforge
mirrorlist = http://mirrorlist.repoforge.org/el6/mirrors-rpmforge
enabled = 1
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1

[rpmforge-extras]
name = RHEL $releasever - RPMforge.net - extras
baseurl = https://mirrors.tuna.tsinghua.edu.cn/repoforge/redhat/el6/en/$basearch/extras
mirrorlist = http://mirrorlist.repoforge.org/el6/mirrors-rpmforge-extras
enabled = 0
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1

[rpmforge-testing]
name = RHEL $releasever - RPMforge.net - testing
baseurl = https://mirrors.tuna.tsinghua.edu.cn/repoforge/redhat/el6/en/$basearch/testing
mirrorlist = http://mirrorlist.repoforge.org/el6/mirrors-rpmforge-testing
enabled = 0  
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1
EOF
```
向系统中添加 Repoforge 的 GPG 公钥：
```
rpm --import http://apt.sw.be/RPM-GPG-KEY.dag.txt
```

##### 2. 修改库配置
```
$ vim /etc/yum.repos.d/rpmforge.repo
```
##### 3. 关闭激活状态
```
[rpmforge]
...
enabled = 0
```

## III 安装 Sclo-RH 源

```
$ cat > /etc/yum.repos.d/rpmforge.repo << EOF
# CentOS-SCLo-rh.repo
#
# Please see http://wiki.centos.org/SpecialInterestGroup/SCLo for more
# information

[centos-sclo-rh]
name=CentOS-$releasever - SCLo rh
baseurl=http://mirror.centos.org/centos/$releasever/sclo/$basearch/rh/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

[centos-sclo-rh-testing]
name=CentOS-$releasever - SCLo rh Testing
baseurl=http://buildlogs.centos.org/centos/$releasever/sclo/$basearch/rh/
gpgcheck=0
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

[centos-sclo-rh-source]
name=CentOS-6 - SCLo rh Sources
baseurl=http://vault.centos.org/centos/$releasever/sclo/Source/rh/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

[centos-sclo-rh-debuginfo]
name=CentOS-$releasever - SCLo rh Debuginfo
baseurl=http://debuginfo.centos.org/centos/$releasever/sclo/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo
EOF

```

# 其它版本的软件仓库

将上例的资源网址，去掉文件名，即可显示其它版本的仓库
比如：
- http://dl.fedoraproject.org/pub/epel/7/x86_64/e/
- http://rpms.famillecollet.com/enterprise
- http://pkgs.repoforge.org/rpmforge-release

可以自行选择对应系统版本的仓库
