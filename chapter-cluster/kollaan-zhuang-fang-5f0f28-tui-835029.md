# Kolla 安装Openstack

> 参考：https://docs.openstack.org/kolla-ansible/latest/user/quickstart.html

## 安装ansible等必要组件

```
$ yum install python-devel libffi-devel gcc openssl-devel libselinux-python python3

$ python -m pip install --upgrade pip

$ pip install ansible
```

## 安装Kolla

```
$ cd ~

# CentOS 7 使用 9.1.1 
# CentOS 8 可以使用最新的版本
$ pip install kolla-ansible==9.1.1

$ mkdir -p /etc/kolla
$ cp -r /usr/local/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
$ cp /usr/local/share/kolla-ansible/ansible/inventory/* .

```

如果是开发者模式请使用下面的安装方式

```
$ cd ~
$ git clone https://github.com/openstack/kolla
$ git clone https://github.com/openstack/kolla-ansible

$ pip install ./kolla
$ pip install ./kolla-ansible

$ cp -r kolla-ansible/etc/kolla/* /etc/kolla
$ cp kolla-ansible/ansible/inventory/* .
```

## 配置Ansible

```
$ vim /etc/ansible/ansible.cfg
```

```
[defaults]
host_key_checking=False
pipelining=True
forks=100
```

## 配置机器结构

如果在一台机器上面安装使用`~/all-in-one`，如果多台，则使用`~/multinode`

```
$ vim ~/multinode
```

参考如下内容

> 带`children`的参数可以配置类似 `control`、`compute` 的值，表示和`control`的值相同。如果不带`children` 则必须写完整的`Host`和用户密码
>
> 如下文的
> `[network:children]` 可以写作 `[network]` 那么就需要写完整的`host`配置

```
[control]
10.0.0.[10:12] ansible_user=root ansible_password=foobar ansible_become=false
# Ansible supports syntax like [10:12] - that means 10, 11 and 12.
# Become clause means "use sudo".

[network:children]
control
# when you specify group_name:children, it will use contents of group specified.

[compute]
10.0.0.[13:14] ansible_user=root ansible_password=foobar ansible_become=true

[monitoring]
10.0.0.10
# This group is for monitoring node.
# Fill it with one of the controllers' IP address or some others.

[storage:children]
compute

[deployment]
localhost       ansible_connection=local become=true
# use localhost and sudo
```

通过`ansible -i multinode all -m ping`测试配置是否通畅

## 生成密码

密码会修改到`/etc/kolla/passwords.yml`

```
$ kolla-genpwd
```

如果是开发者模式，使用如下方式

```
$ cd kolla-ansible/tools
$ ./generate_passwords.py
```

## 配置主信息

```
$ vim /etc/kolla/globals.yml
```

```
kolla_base_distro: "centos"

# 表示yum安装
kolla_install_type: "binary"

# openstack 的版本
openstack_release: ""

# 网卡名，比如enp3s0
network_interface: "eth0"

neutron_external_interface: "eth1"


kolla_enable_tls_internal: "no"

kolla_enable_tls_external: "no"

kolla_internal_vip_address: "10.1.0.250"

kolla_external_vip_address: "{{ kolla_internal_vip_address }}"

# 按需启动相关组件 https://docs.openstack.org/kolla-ansible/latest/reference/index.html
enable_*: "yes"
```

## 创建lvm

在 compute机器上面执行这个

```
$ dd if=/dev/zero of=/cinder-volumes bs=1 count=0 seek=100G
$ losetup -fv /cinder-volumes
$ losetup -l
$ vgcreate cinder-volumes /dev/loop0
$ vgdisplay cinder-volumes
```

删除 cinder-volumes

```
losetup -d /dev/loop0
vgremove cinder-volumes
```



## 开始部署

配置文件在 /usr/local/share/kolla-ansible/ansible/roles/


### Bootstrap服务器部署依赖项

```
kolla-ansible -i ./multinode bootstrap-servers -vvv
```

### 对主机进行部署前检查

```
kolla-ansible -i ./multinode prechecks
```

### 开始部署

```
kolla-ansible -i ./multinode deploy
```
