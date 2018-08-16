MongoDB 数据迁移
===

## 使用Mongo自带的mongodump和mongorestore工具

```
/usr/local/mongodb/bin/mongodump --username anhaoroot --password kele --host 112.124.112.128 --port 27017 --db anhao --out ./

/usr/local/mongodb/bin/mongodump --username root --password THdXQV1rEU918V0m --host 112.124.112.128 --port 27017 --db admin --out ./
```

/usr/local/mongodb/bin/mongodump --username anhaoadmin --password anhaoadmin321 --host 114.215.196.126 --port 27017 --db anhao --out ./


./mongorestore -d anhao  /home/anhao_root/anhao



#### mongodump：

命令格式：mongodump -h dbhost  -d dbname -o dbdirectory

-h:  **mongodb所在服务器地址，例如127.0.0.1，也可以指定端口:127.0.0.1:8080 

-d:  需要备份的数据库名称，例如：test_data

-o:  备份的数据存放的位置，例如：/home/bak

-u:  用户名称，使用权限验证的mongodb服务，需要指明导出账号

-p：用户密码，使用权限验证的mongodb服务，需要指明导出账号密码

#### mongorestore：

命令格式：mongorestore -h dbhost -d dbname -directoryperdb dbdireactory

**-h:  **mongodb所在服务器地址

**-d:  **需要恢复备份的数据库名称，例如：test_data，可以跟原来备份的数据库名称不一样

**-directoryperdb:** 备份数据所在位置，例如：/home/bak/test

**-drop:** 加上这个参数的时候，会在恢复数据之前删除当前数据；

作者：小直
链接：https://www.jianshu.com/p/4b887e5c3e7c
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



