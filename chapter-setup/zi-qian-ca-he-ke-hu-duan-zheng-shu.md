# CA根证书

## CA文件夹
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

## 浏览器证书

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

## nginx配置示例
```
ssl on;
ssl_certificate ~/certs/nginx.crt;
ssl_certificate_key ~/certs/nginx.key;
```


# SAN + CA 证书

## 新建req.conf 申请CA根证书的配置文件
```
[ req ]
distinguished_name  = req_distinguished_name
x509_extensions     = root_ca

[ req_distinguished_name ]

# 以下内容可随意填写
countryName             = CN
countryName_min         = 2
countryName_max         = 2
stateOrProvinceName     = ZheJiang
localityName            = HangZhou
0.organizationName      = Mycompany
organizationalUnitName  = technology 
commonName              = develop 
commonName_max          = 64
emailAddress            = xxxxxxxx@gmail.com 
emailAddress_max        = 64

[ root_ca ]
basicConstraints            = critical, CA:true
```
## 新建domain.ext 生成服务器证书的扩展配置文件
```
subjectAltName = @alt_names
extendedKeyUsage = serverAuth
[alt_names]
# 域名，如有多个用DNS.2,DNS.3…来增加
DNS.1 = domain.com 
# IP地址
IP.1 = 192.168.1.2
IP.2 = 127.0.0.1
```

## 生成证书
```
# 根证书的 key 和 crt
openssl req -x509 -newkey rsa:2048 -out ca.crt -outform PEM -keyout ca.key -days 10000 -verbose -config req.cnf -nodes -sha256 -subj "/CN=MyCompany CA"

# nginx的 key 和 csr
openssl req -newkey rsa:2048 -keyout nginx.key -out nginx.csr -subj /CN=domain.com 
 -sha256 -nodes
    
# 和CA一起颁发 CRT 证书
$ openssl x509 -req -CA ca.crt -CAkey ca.key -in nginx.csr -out nginx.crt -days 10000 -extfile domain.ext -sha256 -set_serial 0x1111
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