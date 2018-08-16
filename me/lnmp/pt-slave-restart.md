

pt-slave-restart

# 1. 安装
yum install -y perl perl-devel perl-Time-HiRes perl-DBI perl-DBD-MySQL perl-IO-Socket-SSL perl-TermReadKey perl-Digest-MD5

wget https://www.percona.com/downloads/percona-toolkit/3.0.10/binary/redhat/7/x86_64/percona-toolkit-3.0.10-1.el7.x86_64.rpm

rpm -i percona-toolkit-3.0.10-1.el7.x86_64.rpm

# 2. 

pt-slave-restart --socket=/tmp/mysql.sock --log='/root/pt-slave-restart.log' --error-numbers=1062 --daemonize --host='localhost' --password='TtASmOOPAs6vl5W9CYLXBUXO683QV^6' --user='root'

pt-slave-restart --socket=/tmp/mysql.sock --log='/root/pt-slave-restart.log' --error-numbers=1032 --daemonize --host='localhost' --password='TtASmOOPAs6vl5W9CYLXBUXO683QV^6' --user='root'

pt-slave-restart --socket=/tmp/mysql.sock --log='/root/pt-slave-restart.log' --daemonize --host='localhost' --password='TtASmOOPAs6vl5W9CYLXBUXO683QV^6' --user='root'
