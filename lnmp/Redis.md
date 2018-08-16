Redis
===

redis 4.0.8

## 1. 下载
https://redis.io/download (此文档底部有安装方法)
wget http://download.redis.io/releases/redis-4.0.8.tar.gz

tar -zxf redis-src.tar.gz

## 2. 编译安装,编译后的二进制文件在源码目录里面
make

## 3. 安装
make install (make install命令执行完成后，会在/usr/local/bin目录下生成本个可执行文件)
mkdir -p /usr/local/redis-4.0.8/bin
mkdir -p /usr/local/redis-4.0.8/etc
ln -s /usr/local/redis-4.0.8 /usr/local/redis
mv /usr/local/bin/redis-* /usr/local/redis/bin/
ln -s /usr/local/redis/bin/{redis-cli,redis-server} /usr/local/bin


make install命令执行完成后，会在/usr/local/bin目录下生成本个可执行文件，分别是redis-server、redis-cli、redis-benchmark、redis-check-aof 、redis-check-dump，redis-sentinel它们的作用如下：

redis-server：Redis服务器的daemon启动程序

redis-cli：Redis命令行操作工具。也可以用telnet根据其纯文本协议来操作

redis-benchmark：Redis性能测试工具，测试Redis在当前系统下的读写性能

redis-check-aof：数据修复

redis-check-dump：检查导出工具

redis-sentinel：Redis哨兵


## 4. 配置文件
cp src/redis.conf /usr/local/redis/etc
cp src/sentinel.conf /usr/local/redis/etc
ln -s /usr/local/redis/etc/* /usr/local/etc

## 5. 修改配置文件，具体修改参见 7
vim /usr/local/redis/etc/redis.conf
配置密码，监听端口，备份文件地址，等等。


## 6. 启动服务
/usr/local/bin/redis-server /usr/local/redis/etc/redis.conf

ps -ef | grep redis


password : N-N(9%N:}-3X  dev
password : @y(l6?Nq%dAQ<*+k  PRD


## 7．修改redis配置文件

a) $ cd /etc

b) vi redis.conf

c) 修改daemonize yes---目的使进程在后台运行

参数介绍：

daemonize：是否以后台daemon方式运行

pidfile：pid文件位置

port：监听的端口号

timeout：请求超时时间

loglevel：log信息级别

logfile：log文件位置

databases：开启数据库的数量

save * *：保存快照的频率，第一个*表示多长时间，第二个*表示执行多少次写操作。在一定时间内执行一定数量的写操作时，自动保存快照。可设置多个条件。

rdbcompression：是否使用压缩

dbfilename：数据快照文件名（只是文件名，不包括目录）

dir：数据快照的保存目录（这个是目录）

appendonly：是否开启appendonlylog，开启的话每次写操作会记一条log，这会提高数据抗风险能力，但影响效率。

appendfsync：appendonlylog如何同步到磁盘（三个选项，分别是每次写都强制调用fsync、每秒启用一次fsync、不调用fsync等待系统自己同步）

## 8． 修改系统配置文件，执行命令

a) echo vm.overcommit_memory=1 >> /etc/sysctl.conf

b) sysctl vm.overcommit_memory=1 或执行echo vm.overcommit_memory=1 >>/proc/sys/vm/overcommit_memory

使用数字含义：

0，表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
1，表示内核允许分配所有的物理内存，而不管当前的内存状态如何。
2，表示内核允许分配超过所有物理内存和交换空间总和的内存



