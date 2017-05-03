# Cygwin 编译

## PHP 编译参数

```
$ ./configure --prefix=/usr --exec-prefix=/usr --localstatedir=/var --sysconfdir=/etc --docdir=/usr/share/doc/php --htmldir=/usr/share/doc/php/html -C --cache-file=../config.cache --libdir=/usr/lib/php --datadir=/usr/share/php --disable-static --disable-rpath --with-config-file-path=/etc --with-config-file-scan-dir=/etc/php.d --with-layout=GNU --with-system-tzdata --enable-dom=yes --enable-filter=yes --enable-hash=yes --with-mhash --enable-libxml=yes --with-openssl=yes --with-system-ciphers --enable-pdo=yes --with-readline=no --with-libedit=yes --enable-session=yes --enable-xml=yes --with-libxml-dir=/usr --with-openssl-dir=/usr --with-pcre-dir=/usr --with-pcre-regex=/usr ac_cv_func_ttyname_r_works=yes --without-pear --enable-bcmath=shared --with-bz2=shared,/usr --enable-calendar=shared --enable-ctype=shared --with-curl=shared,/usr --disable-dba --disable-gdbm --disable-ndbm --disable-db4 --disable-dbm --disable-cdb --enable-inifile --enable-flatfile --with-enchant=shared,/usr --enable-exif=shared --enable-fileinfo=shared --enable-ftp=shared --with-gd=shared,/usr --with-jpeg-dir=/usr --with-png-dir=/usr --with-zlib-dir=/usr --with-xpm-dir=/usr --with-freetype-dir=/usr --enable-gd-native-ttf --with-gettext=shared --with-gmp=shared,/usr --with-iconv=shared,/usr --with-imap=no --with-kerberos=no --with-imap-ssl=no --with-interbase=no --enable-json=shared --disable-ldap --disable-ldap-sasl  --enable-intl=shared --with-icu-dir=/usr --enable-mbstring=shared --with-onig=/usr --enable-mbregex=yes --enable-mbregex_backtrack=yes --with-mcrypt=shared,/usr --with-mysql-sock=/var/run/mysql.sock --with-mysqli=shared,mysqlnd --enable-embedded_mysqli=no --with-oci8=no --with-adabas=no --with-sapdb=no --with-solid=no --with-ibm-db2=no --with-ODBCRouter=no --with-empress=no --with-empress-bcs=no --with-birdstep=no --with-custom-odbc=no --with-iodbc=shared,/usr --with-esoob=no --with-unixODBC=no --with-dbmaker=no --enable-opcache=shared --enable-pcntl=no --with-pdo-dblib=no --with-pdo-firebird=no --with-pdo-mysql=shared,mysqlnd --with-pdo-oci=no --with-pdo-odbc=no --with-pdo-pgsql=no,/usr --with-pdo-sqlite=shared,/usr --with-pgsql=no --enable-phar=shared --enable-posix=shared --with-pspell=no --with-mm=no --enable-shmop=no --enable-simplexml=shared --with-snmp=no --enable-soap=no --with-sqlite3=shared,/usr --enable-sockets=shared --enable-sysvmsg=shared --enable-sysvsem=shared --enable-sysvshm=shared --with-tidy=no --enable-tokenizer=shared --enable-wddx=shared --enable-xmlreader=shared --with-xmlrpc=shared --with-iconv-dir=/usr --enable-xmlwriter=shared --with-xsl=shared,/usr --enable-zip=shared --with-libzip=/usr --with-zlib=shared --enable-cgi --enable-cli --enable-fpm --with-fpm-acl --enable-embed --enable-phpdbg LDFLAGS= LIBS= CPPFLAGS=
```

## PHP-CPP 
## Makefile 修改

将`${PHP_COMPILER_FLAGS}`添加到编译`${PHP_SHARED_LIBRARY}`下，不然报错

```
${PHP_SHARED_LIBRARY}: shared_directories ${COMMON_SHARED_OBJECTS} ${PHP_SHARED_OBJECTS}
	${LINKER} ${PHP_LINKER_FLAGS} ${PHP_COMPILER_FLAGS} -Wl,-soname,libphpcpp.so.$(SONAME) -o $@ ${COMMON_SHARED_OBJECTS} ${PHP_SHARED_OBJECTS}
```