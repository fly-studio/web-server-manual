# 普通证书

## 创建CA文件夹

```
$ mkdir -p ~/certs/ca
$ cd ~/certs/ca
```

## CA证书

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

## ## 客户端证书

简单的nginx的证书

```
$ cd ~/certs/
# 私钥
$ openssl genrsa -out nginx.key 2048
# 公钥
$ openssl req -new -key nginx.key -out nginx.csr
```
**Common Name**  必须是你的域名，支持IP或者泛域名

连接到刚才的CA证书，并颁发最终的目标证书

```
$ openssl x509 -req -days 365 -in nginx.csr -CA ~/certs/ca/cacert.pem -CAkey ~/certs/ca/cakey.pem -CAcreateserial -out nginx.crt
```






