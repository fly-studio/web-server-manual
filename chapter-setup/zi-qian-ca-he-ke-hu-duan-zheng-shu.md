# CA根证书

## 新建 CA文件夹
```
$ mkdir -p ~/certs/ca
$ cd ~/certs/ca
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

## 临时证书
也就是需要将crt文件当做根证书导入到系统

```
openssl req -x509 -out nginx.crt -keyout nginx.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=www.domain.com' -extensions EXT -config <( \
   printf "[dn]\nCN=www.domain.com\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:www.domain.com\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

## CA颁发的证书

nginx的证书
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
## SAN + CA 证书

新建一个文件req.conf
```
[ca]
default_ca = req
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
C = US
ST = VA
L = SomeCity
O = MyCompany
OU = MyDivision
CN = www.company.com
[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1 = www.company.net
DNS.2 = company.com
DNS.3 = company.net
```
上面生成证书请求时的几个字段的意义：

```
C  => Country
ST => State
L  => City
O  => Organization
OU => Organization Unit
CN => Common Name (证书所请求的域名)
emailAddress => main administrative point of contact for the certificate
```

生成证书
```

# CSR 文件
$ openssl req -new -sha256 \
    -key nginx.key \
    -config req.conf -extensions 'v3_req'\
    -out nginx.csr
    
# 和CA一起颁发 CRT 证书
$ openssl ca -in nginx.csr \
    -md sha256 \
    -days 365 -in nginx.csr -cert ~/certs/ca/cacert.pem -keyfile ~/certs/ca/cakey.pem \
    -config req.conf -extensions 'v3_req'\
    -out nginx.crt
```

## nginx配置示例
```
ssl on;
ssl_certificate ~/certs/nginx.crt;
ssl_certificate_key ~/certs/nginx.key;
```
# 其它

## 查看证书
```
$ openssl x509 -text -noout -in cacert.crt
```

## 错误

### 错误1
文件不存在
```
$ touch /etc/pki/CA/index.txt
$ echo '1000' > /etc/pki/CA/serial
```

### 错误2
```
The mandatory stateOrProvinceName field was missing
```
`/etc/pki/tls/openssl.cnf`将 `match` 改为 `optional`
```
# For the CA policy
[ policy_match ]
countryName        = optional 
stateOrProvinceName    = optional
organizationName    = optional
organizationalUnitName    = optional
commonName        = supplied
emailAddress        = optional
```

### 错误3
升级openssl

```
ERROR:Serial number 01 has already been issued,
      check the database/serial_file for corruption
```
### 错误4
升级openssl
```
failed to update database
TXT_DB error number 2
```


## 浏览器

### Chrome下，可以启用本地的证书
```
chrome://flags/#allow-insecure-localhost
```