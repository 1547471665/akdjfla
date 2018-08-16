Redmine
===

http://www.redmine.org/projects/redmine/wiki/RedmineInstall
https://www.howtoforge.com/tutorial/how-to-install-redmine-3-with-nginx-on-centos-7/
https://www.howtoforge.com/tutorial/how-to-install-redmine-with-nginx-on-ubuntu/
https://forums.freebsd.org/threads/howto-redmine-nginx-passenger-postgresql.41256/
http://www.cnblogs.com/csharpsharper/p/3250913.html
http://blog.csdn.net/csfreebird/article/details/9449391
https://www.cnblogs.com/liu-shuai/p/6098328.html
https://www.nginx.com/resources/wiki/start/topics/recipes/redmine/?highlight=redmine
https://www.nginx.com/resources/wiki/start/topics/examples/simplerubyfcgi/
https://www.cnblogs.com/grimm/p/5603310.html
https://forums.freebsd.org/threads/howto-redmine-nginx-passenger-postgresql.41256/
http://www.untrustedconnection.com/2016/05/nginx-https-with-lets-encrypt-and.html
http://www.redmine.org/projects/redmine/wiki/HowTos
http://www.redmine.org/projects/redmine/wiki/HowTo_configure_Nginx_to_run_Redmine
http://www.redmine.org/projects/redmine/search?utf8=%E2%9C%93&wiki_pages=1&q=nginx
http://www.redmine.org/boards/2/topics/37072
https://stackoverflow.com/questions/18778544/cannot-start-thin-server-ruby-gem
http://www.untrustedconnection.com/2016/04/redmine-passenger-and-nginx-on-ubuntu.html



## 0. Require
MySQL
Ruby：yum install ruby ruby-devel

## 1. 下载
wget  -P /data/pkg/ http://www.redmine.org/releases/redmine-3.4.4.tar.gz

tar -zxf /data/pkg/redmine-3.4.4.tar.gz -C /data/pkg/
mkdir -p /data/wwwroot
mv /data/pkg/redmine-3.4.4 /data/wwwroot/


## 2. 创建所需数据库和账号密码(本例使用MySQL5.7)

```
CREATE DATABASE redmine CHARACTER SET utf8mb4;
CREATE USER 'redmine'@'localhost' IDENTIFIED BY '^9@#[Zj6';
GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
```
## 3. 数据库连接配置
cp /data/wwwroot/redmine-3.4.4/config/database.yml.example /data/wwwroot/redmine-3.4.4/config/database.yml

vim /data/wwwroot/redmine-3.4.4/config/database.yml
```
production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: redmine
  password: ^9@#[Zj6
```
## 4. 安装依赖
Redmine uses [Bundler](http://gembundler.com/) to manage gems dependencies.
参见 https://gems.ruby-china.org/

cd /data/wwwroot/redmine-3.4.4/

```
bundle config mirror.https://rubygems.org http://gems.ruby-china.org
gem install bundler
```
Q1: 包源地址被墙了
A1: gem sources --add http://gems.ruby-china.org/ --remove https://rubygems.org/

```
bundle install --without development test postgresql
```
Q1: 
mkmf.rb can't find header files for ruby at /usr/share/include/ruby.h
A1:
yum install ruby-devel

Q2:
An error occurred while installing rmagick (2.16.0), and Bundler cannot continue.
A2:
yum install ImageMagick ImageMagick-devel


## 5. 加密存储Session
```
bundle exec rake generate_secret_token
```
## 6. 创建数据库Schema
```
cd /data/wwwroot/redmine-3.4.4/
RAILS_ENV=production bundle exec rake db:migrate
```
If you get this error with Ubuntu:

Rake aborted!
no such file to load -- net/https

Then you need to install `libopenssl-ruby1.8` just like this: `apt-get install libopenssl-ruby1.8`.

## 8. 初始化数据库默认数据
```
RAILS_ENV=production REDMINE_LANG=en bundle exec rake redmine:load_default_data
```
## 9. 文件系统权限配置
NB: _Windows users can skip this section._

The user account running the application must have write permission on the following subdirectories:

1.  `files` (storage of attachments)
2.  `log` (application log file `production.log`)
3.  `tmp` and `tmp/pdf` (create these ones if not present, used to generate PDF documents among other things)
4.  `public/plugin_assets` (assets of plugins)

E.g., assuming you run the application with a redmine user account:

mkdir -p tmp tmp/pdf public/plugin_assets
sudo chown -R redmine:redmine files log tmp public/plugin_assets
sudo chmod -R 755 files log tmp public/plugin_assets

Note: If you have files in these directories (e.g. restore files from backup), make sure these files are not executable.

sudo find files log tmp public/plugin_assets -type f -exec chmod -x {} +

## 10. 测试安装
nohup bundle exec rails server webrick -e production

Once WEBrick has started, point your browser to [http://localhost:3000/](http://localhost:3000/). You should now see the application welcome page.

> Note: Webrick is **not** suitable for production use, please only use webrick for testing that the installation up to this point is functional. Use one of the many other guides in this wiki to setup redmine to use either Passenger (aka `mod_rails`), FCGI or a Rack server (Unicorn, Thin, Puma, hellip;) to serve up your redmine.

默认账号密码
Use default administrator account to log in:

*   login: admin
*   password: admin   wangnan123


## 11. 配置

配置文件：config/configuration.yml
Redmine settings are defined in a file named `config/configuration.yml`.

If you need to override default application settings, simply copy `config/configuration.yml.example` to `config/configuration.yml` and edit the new file; the file is well commented by itself, so you should have a look at it.

These settings may be defined per Rails environment (`production`/`development`/`test`).

Important : don't forget to restart the application after any change.

## 12. Email / SMTP server settings

Email configuration is described in a [dedicated page](http://www.redmine.org/projects/redmine/wiki/EmailConfiguration).


## 12. 如何配置 Redmine运行在Nginx HowTo configure Nginx to run Redmine


cd /data/wwwroot/redmine/public/

cp dispatch.fcgi.example dispatch.fcgi

cp htaccess.fcgi.example .htaccess

chown -R www.www ./*




```
cd /data/wwwroot/redmine-3.4.4/
cp ./public/dispatch.fcgi.example ./public/dispatch.fcgi
cp ./public/htaccess.fcgi.example public/.htaccess
```

http://www.redmine.org/boards/2/topics/37072



./configure --prefix=/usr/local/nginx-1.12.2/ --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_gzip_static_module --with-http_stub_status_module --with-http_addition_module --with-ngx_http_upstream_module
#server {
#       listen 80;
#       server_name redmine.anhao.cn;
#       index index.html index.htm index.php;
#       root /data/wwwroot/redmine-3.4.4/public;
#
#       access_log /data/log/nginx-1.12.2/redmine.anhao.cn.log main;
#       error_log /data/log/nginx-1.12.2/redmine.anhao.cn.error.log;
#
#       #include none.conf;
#       passenger_enabled on;
#}

如下配置即可，采用代理的方式的。

uptream redmine {
        server 127.0.0.1:3000;
}

server {
        server_name redmine.anhao.cn;
        root /data/wwwroot/redmine;

        access_log /data/log/nginx-1.12.2/redmine.anhao.cn.log main;
        error_log /data/log/nginx-1.12.2/redmine.anhao.cn.error.log;

        location / {
                try_files $uri @ruby;
        }

        location @ruby {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_redirect off;
                proxy_read_timeout 300;
                proxy_pass http://redmine;
        }
}
