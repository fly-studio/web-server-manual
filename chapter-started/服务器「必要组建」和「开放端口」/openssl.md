# OpenSSL

查看现在版本：
```
$ openssl version
```


## ## openSSL 1.1.0

1.1.0 [下载地址](https://www.openssl.org/source/openssl-1.1.0-latest.tar.gz "下载地址")
```
$ wget https://www.openssl.org/source/openssl-1.1.0-latest.tar.gz
$ tar -zxvf openssl-*-latest.tar.gz
$ cd openssl-*
# 安装
$ ./config --prefix=/usr
$ make -j4
$ make install
```

## ## openSSL 1.0.2

> 注意：1.0.2是一个过时的版本，除非软件明确要求安装1.0.2，其它情况下，安装 1.1.0 即可


- 1.0.2需要打ChaCha20/Poly1305的补丁，这两个加密算法对手机支持友好 [cloudflare补丁](https://github.com/travislee8964/sslconfig/tree/master/patches "cloudflare补丁")
- 由于默认 gcc 版本为 4.4.7，在添加 Chacha 20 补丁之后，编译会报错：[#11](https://github.com/cloudflare/sslconfig/issues/11 "#11")，需要将 gcc 升级到 4.8 以上版本：

```
$ wget https://www.openssl.org/source/openssl-1.0.2-latest.tar.gz
$ tar -zxf openssl-*-latest.tar.gz
$ cd openssl-*

# openssl-1.0.2 打补丁
$ wget https://raw.githubusercontent.com/travislee8964/sslconfig/master/patches/openssl__chacha20_poly1305_draft_and_rfc_ossl102i.patch
$ patch -p1 < ./openssl__chacha20_poly1305_draft_and_rfc_ossl102i.patch

# 安装
$ ./config --prefix=/usr
$ make
$ make test
$ make install
```

