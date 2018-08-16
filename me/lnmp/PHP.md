PHP
===
CentOS 7.2
php-7.2.2

mkdir -p /data/pkg && cd /data/pkg

## 1. 添加用户
useradd -r -s /sbin/nologin php-fpm

## 2. 下载
wget http://php.net/get/php-7.2.2.tar.gz/from/this/mirror
mv mirror php-7.2.2.tar.gz

## 3. 安装
* --with-config-file-path=/usr/local/php-7.2.2/etc \ #  设置 php.ini 的搜索路径。默认为 PREFIX/lib。

tar -zxf php-7.2.2.tar.gz
cd php-7.2.2/

./configure \
--prefix=/usr/local/php-7.2.2 \
--with-mhash \
--with-openssl \
--with-config-file-path=/usr/local/php-7.2.2/etc \
--disable-short-tags \
--enable-fpm \
--with-fpm-user=php-fpm \
--with-fpm-group=php-fpm \
--enable-xml \
--with-libxml-dir \
--enable-bcmath \
--enable-calendar \
--enable-intl \
--enable-mbstring \
--enable-pcntl \
--enable-shmop \
--enable-soap \
--enable-sockets \
--enable-zip \
--enable-mbregex \
--enable-mysqlnd \
--enable-mysqlnd-compression-support \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-gd \
--enable-ftp \
--with-curl \
--with-xsl \
--with-iconv \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--enable-sysvsem \
--enable-inline-optimization \
--with-xmlrpc \
--with-gettext

make -j 4 && make install

Q1: configure: error: xml2-config not found. Please check your libxml2 installation.
A1: yum install -y libxml2-devel

Q2: checking for cURL 7.10.5 or greater... configure: error: cURL version 7.10.5 or later is required to compile php with cURL support
A2: yum -y install libcurl-devel

Q3: configure: error: jpeglib.h not found.
A3: yum -y install libjpeg-devel

Q4: configure: error: png.h not found.
A4: yum -y install libpng-devel
 
Q5: configure: error: freetype-config not found.
A5: yum install -y freetype-devel

Q6: configure: error: Unable to detect ICU prefix or no failed. Please verify ICU install prefix and make sure icu-config works.
A6: yum install -y libicu-devel

Q7: configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution.
A7: yum -y install libxslt-devel

Q8: configure: error: Cannot find OpenSSL's <evp.h>
A8: yum -y install openssl-devel

Q9: configure: error: Unable to detect ICU prefix or no failed. Please verify ICU install prefix and make sure icu-config works.
A9: yum -y install libicu-devel

ln -s /usr/local/php-7.2.2/ /usr/local/php
ln -s /usr/local/php/bin/php /usr/local/bin
ln -s /usr/local/php/sbin/php-fpm /usr/local/sbin


cd /data/pkg/php-7.2.2
cp ./php.ini-development ./php.ini-production  /usr/local/php/etc
cp /usr/local/php/etc/php.ini-development /usr/local/php/etc/php.ini
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf

ln -s /usr/local/php/etc/  /usr/local/etc/php

/usr/local/sbin/php-fpm










