MongoDB (CentOS 7.2)
===
https://docs.mongodb.com/manual/tutorial/install-mongodb-on-linux/
https://docs.mongodb.com/manual/


## 1. 下载
wget -P /data/pkg/ https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.6.2.tgz

tar -zxf /data/pkg/mongodb-linux-x86_64-rhel70-3.6.2.tgz -C /data/pkg/

## 2. 安装
mv mongodb-linux-x86_64-rhel70-3.6.2 /usr/local/
ln -s /usr/local/mongodb-linux-x86_64-rhel70-3.6.2 /usr/local/mongodb
ln -s /usr/local/mongodb/bin/{mongo,mongod} /usr/local/bin/

## 3. 创建数据和日志目录
mkdir -p /data/db/mongodb-3.6.2/
mkdir -p /data/log/mongodb-3.6.2/

## 4. 配置
https://docs.mongodb.com/manual/reference/configuration-options/

mkdir /usr/local/mongodb/etc/
touch /usr/local/mongodb/etc/mongod.conf
ln -s /usr/local/mongodb/etc/mongod.conf /usr/local/etc/

vim /usr/loca/etc/mongod.conf

```
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /data/db/mongodb-3.6.2
  journal:
    enabled: true
#  engine:
#  mmapv1:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /data/log/mongodb-3.6.2/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo
  fork: true

#security:
security:
  authorization: enabled

#operationProfiling:

#replication:

#sharding:

## Enterprise-Only Options:

#auditLog:

#snmp:

```

## 5. 启动
/usr/local/bin/mongod --config /usr/local/etc/mongod.conf


db.createUser(
   {
     user: "root",
     pwd: "+68%<UQ*fB&#tr}!",
     roles: [ "readWrite", "dbAdmin" ]
   }
);

db.createUser(
   {
     user: "developer",
     pwd: "*~^@6]<#",
     roles: [ "readWrite" ]
   }
)


db.createUser(
   {
     user: "anhao",
     pwd: "+86%<Q&U*BF#tr}!",
     roles: [ "readWrite" ]
   }
)


db.auth('root', '+68%<UQ*fB&#tr}!');
