# CA根证书

新建 CA文件夹
```
$ mkdir ~/ca
$ cd ~/ca
```

新建CA证书
```
# 私钥
$ openssl genrsa -out cakey.pem 2048
# 公钥
$ openssl req -new -x509 -key cakey.pem -out cacert.pem
```
因为是根证书，资料不重要，可以按需填写

## 导入系统的根证书

### 导入到Linux系统
```
$ cp /etc/pki/tls/certs/ca-bundle.crt{,.bak}    备份以防出错
$ cat ~/ca/cacert.pem >> /etc/pki/tls/certs/ca-bundle.crt
```
### 导入到Windows系统
将cacert.pem复制并改名为cacert.crt，双击，并安装到受信任的根证书颁发机构

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

连接到刚才新建的CA证书，并颁发最终的目标证书
```
$ openssl x509 -req -in nginx.csr -CA ~/ca/cacert.pem -CAkey ~/ca/cakey.pem -CAcreateserial -out nginx.crt
```

nginx配置的
```
ssl on;
ssl_certificate ~/nginx.crt;
ssl_certificate_key ~/nginx.key;
```