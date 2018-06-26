如何制作证书请参考下级分类


## 导入系统的根证书

### 导入到Linux系统
```
$ cp /etc/pki/tls/certs/ca-bundle.crt{,.bak}    备份以防出错
$ cat ~/ca/cacert.pem >> /etc/pki/tls/certs/ca-bundle.crt
```
### 导入到Windows系统
将`cacert.pem`复制并改名为`cacert.crt`，双击，并安装到受信任的根证书颁发机构

![](/assets/ie_certificate_pathpng.png)



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

### Chrome下，可以启用本地location证书
```
chrome://flags/#allow-insecure-localhost
```