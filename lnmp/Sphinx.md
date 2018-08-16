Sphinx
===

## 1. 下载
wget http://sphinxsearch.com/files/sphinx-3.0.2-2592786-linux-amd64.tar.gz
tar -zxf /data/pkg/sphinx-3.0.2-2592786-linux-amd64.tar.gz -C /data/pkg/

mv /data/pkg/sphinx-3.0.2-2592786-linux-amd64  /usr/local/
mv /usr/local/sphinx-3.0.2-2592786-linux-amd64 /usr/local/sphinx-3.0.2

ln -s /usr/local/sphinx-3.0.2 /usr/local/sphinx

ln -s /usr/local/sphinx/bin/searchd /usr/local/bin/
ln -s /usr/local/sphinx/bin/indexer /usr/local/bin/


## 2. 创建索引数据
/usr/local/bin/indexer --config  /usr/local/etc/sphinx-min.conf  --all --rotate

Q:
ERROR: index 'anhao_drug': sql_connect: failed to load libmysqlclient (or libmariadb) (DSN=mysql://developer:***@localhost:3306/anhao_development).
A:
cd /etc/ld.so.conf.d/
随便复制其中一个，然后加载 /usr/local/mysql/lib
ldconfig（生效命令）

## 3. 启动服务
/usr/local/bin/searchd --config /usr/local/etc/sphinx-min.conf --pidfile


