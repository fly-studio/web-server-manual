# CA根证书

## 新建 CA文件夹
```
$ mkdir ~/ca
$ cd ~/ca
```

## 新建CA证书
因为是根证书，资料不重要，可以按需填写，注意**Common Name** 会显示在证书颁发者

### 方法一
两步
```
# 私钥
$ openssl genrsa -out cakey.pem 2048
# 证书
$ openssl req -days 365 -new -x509 -key cakey.pem -out cacert.pem
```
### 方法二
通过csr文件再生成证书
```
# 私钥
$ openssl genrsa -out cakey.pem 2048
# 公钥 certificate signing request
$ openssl req -new -key cakey.pem -out cacert.csr
# 证书
$ openssl x509 -req -days 365 -in cacert.csr -signkey cakey.pem -out cacert.pem
```
### 方法三
一次生成KEY和证书
```
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout cakey.pem -out cacert.pem
```


## 导入系统的根证书

### 导入到Linux系统
```
$ cp /etc/pki/tls/certs/ca-bundle.crt{,.bak}    备份以防出错
$ cat ~/ca/cacert.pem >> /etc/pki/tls/certs/ca-bundle.crt
```
### 导入到Windows系统
将`cacert.pem`复制并改名为`cacert.crt`，双击，并安装到受信任的根证书颁发机构

![](/assets/ie_certificate_pathpng.png)


# 客户端证书
比如新建nginx的证书

```
$ cd ~
# 私钥
$ openssl genrsa -out nginx.key 2048
# 公钥
$ openssl req -new -key nginx.key -out nginx.csr
```

**Common Name**  必须是你的域名，支持IP或者泛域名

## 颁发证书 nginx.crt

连接到刚才的CA证书，并颁发最终的目标证书
```
$ openssl x509 -req -days 365 -in nginx.csr -CA ~/ca/cacert.pem -CAkey ~/ca/cakey.pem -CAcreateserial -out nginx.crt
```

nginx配置示例
```
ssl on;
ssl_certificate ~/nginx.crt;
ssl_certificate_key ~/nginx.key;
```