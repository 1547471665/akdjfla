MySQL主从记录
===

/usr/local/mysql/bin/mysqldump -uroot -h1.mysql.anhao -p --set-gtid-purged=ON  --single-transaction --all-databases > all_databases.sql

change master to MASTER_HOST='1.mysql.anhao', master_user='root', master_password='TtASmOOPAs6vl5W9CYLXBUXO683QV^6', master_port=3306,  master_auto_position=0;

change master to MASTER_HOST='1.mysql.anhao', master_user='root', master_password='TtASmOOPAs6vl5W9CYLXBUXO683QV^6', master_port=3306,  master_auto_position=1;

start slave;

show slave status;


