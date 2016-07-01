# 查看服务器环境各软件编译参数

>    注意，示例中给出的某些软件安装路径可能与您机器安装路径不一致，请自行替换路径。

## 查看 `nginx` 编译参数

对于已安装好的 `nginx` ，可以通过以下方式查看：

```bash
/usr/local/nginx/sbin/nginx -V
```

最后返回的结果类似如：

```bash
root@localhost:~# /usr/local/nginx/sbin/nginx -V
nginx version: nginx/1.6.1
built by gcc 4.8.2 (Ubuntu 4.8.2-19ubuntu1) 
TLS SNI support enabled
configure arguments: --prefix=/usr/local/nginx --user=www --group=www --with-http_stub_status_module --with-http_ssl_module --with-http_flv_module --with-http_gzip_static_module --with-ld-opt='-ljemalloc' --add-module=../ngx_pagespeed-1.8.31.4-beta --with-cc-opt='-DLINUX=2 -D_REENTRANT -D_LARGEFILE64_SOURCE -pthread'
```

## 查看 `Apache` 编译参数

对于已安装好的 `Apache` ，可以通过以下方式查看：

```bash
cat /usr/local/apache/build/config.nice
```

最后返回的结果类似如：

```bash
root@localhost:~# cat /usr/local/apache/build/config.nice
#! /bin/sh
#
# Created by configure

"./configure" \
"--prefix=/usr/local/apache" \
"--enable-headers" \
"--enable-deflate" \
"--enable-mime-magic" \
"--enable-so" \
"--enable-rewrite" \
"--enable-ssl" \
"--with-ssl" \
"--enable-expires" \
"--enable-static-support" \
"--enable-suexec" \
"--with-included-apr" \
"--with-mpm=prefork" \
"--disable-userdir" \
"$@"
```

## 查看 `PHP` 编译参数

参看获取 `PHP` 的编译参数：

```bash
/usr/local/php/bin/php -i |grep configure
```

返回的结果类似如：

```bash
root@localhost:~# /usr/local/php/bin/php -i |grep configure
Configure Command =>  './configure'  '--prefix=/usr/local/php' '--with-config-file-path=/usr/local/php/etc' '--with-apxs2=/usr/local/apache/bin/apxs' '--enable-opcache' '--with-mysql=mysqlnd' '--with-mysqli=mysqlnd' '--with-pdo-mysql=mysqlnd' '--disable-fileinfo' '--with-iconv-dir=/usr/local' '--with-freetype-dir' '--with-jpeg-dir' '--with-png-dir' '--with-zlib' '--with-libxml-dir=/usr' '--enable-xml' '--disable-rpath' '--enable-bcmath' '--enable-shmop' '--enable-exif' '--enable-sysvsem' '--with-curl' '--enable-mbregex' '--enable-inline-optimization' '--enable-mbstring' '--with-mcrypt' '--with-gd' '--enable-gd-native-ttf' '--with-openssl' '--with-mhash' '--enable-pcntl' '--enable-sockets' '--with-xmlrpc' '--enable-ftp' '--with-gettext' '--enable-zip' '--enable-soap' '--disable-ipv6' '--disable-debug' 'CFLAGS=' 'CXXFLAGS='
```

## 查看 `MySQL` 编译参数

查看 `MySQL` 编译参数：

```bash
cat /usr/local/mysql/bin/mysqlbug |grep configure
```

上面语句可能在某些新MySQL安装版本不能回显任何有用信息。


