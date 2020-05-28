# Kolla 安装Openstack

## 安装ansible等必要组件

```
$ yum install python-devel libffi-devel gcc openssl-devel libselinux-python python3

$ python -m pip install --upgrade pip

$ yum install ansible
```

## 安装Kolla

```
$ cd ~

$ pip install kolla-ansible

$ mkdir -p /etc/kolla
$ cp -r /usr/local/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
$ cp /usr/local/share/kolla-ansible/ansible/inventory/* .

```

如果需要修改源码请使用下面的安装方式

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

## 安装前配置

如果在一台机器上面安装使用`~/all-in-one`，如果多台，则使用`~/multinode`

```
vim ~/multinode
```

参考如下内容

> 带`children`的参数可以配置类似 `control` 的值，表示和`control`的值相同。如果不带`children` 则必须写完整的`Host`和用户密码
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
kolla-genpwd
```

如果是开发者模式，使用如下方式

```
cd kolla-ansible/tools
./generate_passwords.py
```