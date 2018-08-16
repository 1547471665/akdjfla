MySQL
===

如果系统默认安装了 MariaDB, 请先卸载。只要看 /etc/my.cnf就可确认是否安装
yum remove mariadb-libs

## 1. Add User
useradd -s /usr/sbin/nologin -r mysql

## 2. 下载
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-boost-5.7.21.tar.gz
tar -zxf mysql-boost-5.7.21.tar.gz
cd mysql-5.7.21

## 3. cmake
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql-5.7.21 \
-DSYSCONFDIR=/usr/local/mysql-5.7.21/etc \
-DMYSQL_DATADIR=/data/mysql \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITH_SYSTEMD=1 \
-DWITH_SSL=system \
-DWITH_ZLIB=system \
-DWITH_EMBEDDED_SERVER=1 \
-DENABLED_LOCAL_INFILE=1 \
-DEXTRA_CHARSETS=all \
-DDEFAULT_CHARSET=utf8mb4 \
-DDEFAULT_COLLATION=utf8mb4_general_ci \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=/usr/local/boost

## 4. cmake 问题解决
Q1: 
	bash: cmake: command not found
A1: 
	yum install -y cmake


Q2: 
	CMake Error at cmake/boost.cmake:81 (MESSAGE):
	  You can download it with -DDOWNLOAD_BOOST=1 -DWITH_BOOST=<directory>
	
	  This CMake script will look for boost in <directory>.  If it is not there,
	  it will download and unpack it (in that directory) for you.
	
	  If you are inside a firewall, you may need to use an http proxy:
	
	  export http_proxy=http://example.com:80
	
	Call Stack (most recent call first):
	  cmake/boost.cmake:238 (COULD_NOT_FIND_BOOST)
	  CMakeLists.txt:507 (INCLUDE)
A2: 
	-DDOWNLOAD_BOOST=1 \
   	-DWITH_BOOST=/usr/local/boost

Q3:
	-- Could NOT find Curses (missing:  CURSES_LIBRARY CURSES_INCLUDE_PATH)
	CMake Error at cmake/readline.cmake:64 (MESSAGE):
	Curses library not found.  Please install appropriate package,remove CMakeCache.txt and rerun cmake.On Debian/Ubuntu, package name is libncurses5-dev, on Redhat and derivates it is ncurses-devel.
	Call Stack (most recent call first):
	  cmake/readline.cmake:107 (FIND_CURSES)
	  cmake/readline.cmake:197 (MYSQL_USE_BUNDLED_EDITLINE)
	  CMakeLists.txt:535 (MYSQL_CHECK_EDITLINE)
	
	-- Configuring incomplete, errors occurred!
	See also "/data/pkg/mysql-5.7.21/CMakeFiles/CMakeOutput.log".
	See also "/data/pkg/mysql-5.7.21/CMakeFiles/CMakeError.log".
A3: 
	yum -y install ncurses-devel
	rm CMakeCache.txt

Q4:
	CMake Warning at cmake/bison.cmake:20 (MESSAGE):
	Bison executable not found in PATH
	Call Stack (most recent call first):
	sql/CMakeLists.txt:502 (INCLUDE)
	
	CMake Warning at cmake/bison.cmake:20 (MESSAGE):
	Bison executable not found in PATH
	Call Stack (most recent call first):
	libmysqld/CMakeLists.txt:133 (INCLUDE)
A4: 
	yum install -y libbison-devel


Q5:
	-- INSTALL mysqlclient.pc lib/pkgconfig
	-- Skipping deb packaging on unsupported platform .
	-- CMAKE_BUILD_TYPE: RelWithDebInfo
	-- COMPILE_DEFINITIONS: _GNU_SOURCE;_FILE_OFFSET_BITS=64;HAVE_CONFIG_H;HAVE_LIBEVENT1
	-- CMAKE_C_FLAGS:  -Wall -Wextra -Wformat-security -Wvla -Wwrite-strings -Wdeclaration-after-statement
	-- CMAKE_CXX_FLAGS:  -Wall -Wextra -Wformat-security -Wvla -Woverloaded-virtual -Wno-unused-parameter
	-- CMAKE_C_LINK_FLAGS:
	-- CMAKE_CXX_LINK_FLAGS:
	-- CMAKE_C_FLAGS_RELWITHDEBINFO: -O3 -g -fabi-version=2 -fno-omit-frame-pointer -fno-strict-aliasing -DDBUG_OFF
	-- CMAKE_CXX_FLAGS_RELWITHDEBINFO: -O3 -g -fabi-version=2 -fno-omit-frame-pointer -fno-strict-aliasing -DDBUG_OFF
	-- Configuring incomplete, errors occurred!
	See also "/data/pkg/mysql-5.7.21/CMakeFiles/CMakeOutput.log".
	See also "/data/pkg/mysql-5.7.21/CMakeFiles/CMakeError.log".

A5: yum -y install openssl-devel

## 5. make && make install
make -j8 && make install

## 6. 目录创建、目录权限、配置文件
ln -s /usr/local/mysql-5.7.21  /usr/local/mysql
ln -s /usr/local/mysql/bin/mysql /usr/local/bin

// 添加配置文件
mkdir /usr/local/mysql/etc
touch /usr/local/mysql/etc/my.cnf
ln -s /usr/local/mysql/etc/my.cnf /usr/local/etc
// 添加配置文件内容，MySQL不提供，需自己写https://github.com/vfhky/mylnmp/blob/master/MySql/my.cnf
http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
vim /usr/local/mysql/etc/my.cnf

// 更改权限
chown -R mysql:mysql /usr/local/mysql/*

// Create data dir and log dir
mkdir -p /data/db/mysql-5.7.21
mkdir -p /data/log/mysql-5.7.21
chown -R mysql:mysql /data/db/mysql-5.7.21 /data/log/mysql-5.7.21

## 7. 初始化数据库，生成数据库文件
// (推荐)--initialize 会生成root密码, 密码在 error.log里面可以看到
// ALTER  USER  'root'@'localhost'  IDENTIFIED  BY  'new_password';
/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/db/mysql-5.7.21
/usr/local/mysql/bin/mysql_ssl_rsa_setup

## 8. 启动MySQL前准备工作
// mysql默认将mysqld.service文件安装到了mysql安装目录下的 usr/lib/systemd/system/，将mysqld.service复制到/usr/lib/systemd/system/目录下
// 复制mysqld.service
cp /usr/local/mysql/usr/lib/systemd/system/mysqld.service /usr/lib/systemd/system

// 因为在mysqld.service，把默认的pid文件指定到了/var/run/mysqld/目录，而并没有事先建立该目录，因此要手动建立该目录并把权限赋给mysql用户。
mkdir -p /var/run/mysqld/
chown mysql:mysql /var/run/mysqld
// 启动MySQL
systemctl start mysqld
Q1:
	Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
A1:
	查看 /data/log/mysql/error.log 错误日志，根据具体错误解决
    
[ERROR] Could not create unix socket lock file /var/lib/mysql/mysql.sock.lock.
mkdir /var/lib/mysql/
chown mysql:mysql /var/lib/mysql

## 9.初始化MySQL数据库的root用户密码
和Oracle数据库一样，MySQL数据库也默认自带了一个root用户（这个和当前Linux主机上的root用户是完全不搭边的），我们在设置好MySQL数据库的安全配置后初始化root用户的密码。配置过程中，一路输入y就行了。这里只说明下MySQL-5.7.9版本中，用户密码策略分成低级LOW、中等MEDIUM和超强STRONG三种，推荐使用中等MEDIUM级别！

// 生成的密码在 error.log日志里面  : root      Lr<sp,ku2PTf   root@localhost: hT0F?n4xrXSi
 
/usr/local/mysql/bin/mysql_secure_installation


mysql1: "Tt%&m`*PAs6vl-W9CY,X<U=O/$3QV^F:"
mysql2: Tt%&m`*PAs6vl-W9CY,X<U=O/$3QV^F:


开发环境密码:
root: THdXQV1rEU918V0m




// 查看用户权限
show grants for 'developer'@'%';

// 查看配置文件路径
/usr/local/mysql/bin/mysqld --verbose --help | grep -A 1 'Default options'


https://dev.mysql.com/doc/refman/5.7/en/using-systemd.html#systemd-overview
systemctl {start|stop|restart|status} mysqld












