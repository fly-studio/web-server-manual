# # 安装
如果你有安装过 elasticsearch.repo，那么直接使用yum安装即可
```bash
$ yum install logstash
```
# # 配置
## ## JAVA的环境变量
```
$ vim /etc/profile
```
```bash
JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
JRE_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.111-1.b15.el7_2.x86_64
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME PATH
```
```
$ source /etc/profile
```
## ## 数据文件夹
```bash
$ mkdir /www/database/logstash/
$ chown -R logstash:logstash /www/database/logstash/
```
## ## 主配置文件
```bash
$ vim /etc/logstash/logstash.yml
```
```
path.data: /www/database/logstash/
```
## ## 运行配置
Logstash的运行依赖运行配置文件，所有的日志、通道配置文件在`/etc/logstash/conf.d/`

比如 L+ 的配置
```bash
$ vim /etc/logstash/conf.d/l+.conf
```
```
input {
  file {
    path => "/www/website/base/storage/logs/logstash-*.log"
    type => "base"
    codec => json {
    }
  }
  # 此处可以添加更多来源
}

filter {

}

output {
  elasticsearch { hosts => ["127.0.0.1:9200"] }
  # 在服务器运行时，下面的可以不用
  stdout { codec => rubydebug }
}
```

# 添加patterns文件夹
```
$ mkdir /etc/logstash/patterns.d/
```
默认的patterns类型：
https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/grok-patterns
```

```


# # 启动
## ## 创建服务
### ### CentOS 7
如果已经安装过`ruby`，以及`gem install pleaserun`
```
$ pleaserun -p systemd -v default --install /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/ --config.reload.automatic
```
如果没有请ruby，参照
```
$ vim /usr/lib/systemd/system/logstash.service
```
内容查看
> https://github.com/guardian/machine-images/blob/master/packer/resources/features/elk-stack/systemd-logstash.service

### ### CentOS 6
```
$ vim /etc/init.d/logstash
```
> 参照 https://gist.githubusercontent.com/piavlo/4538157/raw/0f4c05bfa7c5fece96065b60a19c063d99058cb2/etc%2520init.d%2520logstash
