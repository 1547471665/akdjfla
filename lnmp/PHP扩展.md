## 1. Yar-2.0.4
wget -P /data/pkg/ https://github.com/laruence/yar/archive/yar-2.0.4.zip
unzip /data/pkg/yar-2.0.4.zip -d /data/pkg/
cd /data/pkg/yar-yar-2.0.4/

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --enable-msgpack
make -j && make install


## 2. Yaf-3.0.6
wget -P /data/pkg/ https://github.com/laruence/yaf/archive/yaf-3.0.6.zip
unzip /data/pkg/yaf-3.0.6.zip -d /data/pkg/
cd /data/pkg/yaf-yaf-3.0.6/

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make -j && make install


## 3. MsgPack-2.0.2
wget -P /data/pkg/ https://github.com/msgpack/msgpack-php/archive/msgpack-2.0.2.zip
unzip /data/pkg/msgpack-2.0.2.zip -d /data/pkg/
cd /data/pkg/msgpack-php-msgpack-2.0.2/

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make -j && make install

## 4. SeasLog-1.7.6
wget -P /data/pkg/ https://github.com/SeasX/SeasLog/archive/SeasLog-1.7.6.zip
unzip /data/pkg/SeasLog-1.7.6.zip -d /data/pkg/
cd /data/pkg/SeasLog-SeasLog-1.7.6/

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make -j && make install

## 5. MongoDBLib
cd /data/pkg
git clone https://github.com/mongodb/mongo-php-driver.git
cd mongo-php-driver
git submodule update --init

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make all && make install

## 6. Redis-3.1.6
wget -P /data/pkg/ https://github.com/phpredis/phpredis/archive/3.1.6.zip
mv /data/pkg/3.1.6.zip /data/pkg/phpredis-3.1.6.zip

unzip /data/pkg/phpredis-3.1.6.zip -d /data/pkg/
cd /data/pkg/phpredis-3.1.6/

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --enable-redis-igbinary
make -j && make install

Q: checking for igbinary includes... configure: error: Cannot find igbinary.h
A: 这是没有安装igbinary组件的时候报的错，可以去[php官网](pecl.php.net/package/igbinary) 下载。 
   下载后运行解压缩并进入igbinary运行phpize获得configure，然后./configure –with-php-config=php-config脚本的路径。然后make && make install 安装完成。

## 7. igbinary-2.0.5
wget  -P /data/pkg/ http://pecl.php.net/get/igbinary-2.0.5.tgz

tar -zxf /data/pkg/igbinary-2.0.5.tgz -C /data/pkg/
cd /data/pkg/igbinary-2.0.5/

/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make -j && make install

## 8. Swoole
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --enable-openssl
make -j && make install



















