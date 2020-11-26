---
layout: post
title: 'Linux(Centos 7)环境下安装Nginx'
subtitle: 'Linux(Centos 7)环境下安装Nginx'
date: 2020-11-24
categories: Nginx
tags: Nginx
---

Linux(Centos 7)环境下安装Nginx

需联网安装

1.

```
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```

2.

```
wget https://jaist.dl.sourceforge.net/project/pcre/pcre/8.42/pcre-8.42.tar.gz --no-check-certificate
```

3.或者离线下载 pcre-8.42.tar.gz
4.

```
[root@localhost pcre] tar -zxf pcre-8.42.tar.gz
[root@localhost pcre] cd pcre-8.42
[root@localhost pcre-8.42] ./configure --prefix=/usr/local/pcre
[root@localhost pcre-8.42] make && make install
```

5.

```
[root@localhost nginx] tar -zxf nginx-1.16.0.tar.gz
[root@localhost nginx] cd nginx-1.16.0
[root@localhost nginx-1.16.0] ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=../pcre-8.42 --with-stream
[root@localhost nginx-1.16.0] make && make install
```