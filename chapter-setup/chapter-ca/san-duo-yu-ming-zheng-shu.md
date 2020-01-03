Chrome等浏览器已经强制校验SAN证书的多域名

# SAN + CA 证书

## 新建req.cnf 申请CA根证书的配置文件
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
openssl req -nodes -newkey rsa:2048 -keyout nginx.key -out nginx.csr -subj /CN=domain.com 
 -sha256 -nodes
    
# 和CA一起颁发 CRT 证书
$ openssl x509 -req -CA ca.crt -CAkey ca.key -in nginx.csr -out nginx.crt -days 10000 -extfile domain.ext -sha256 -set_serial 0x1111
```


