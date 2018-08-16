GitLab
===

## 0. 采用docker方式安装

## 1. docker-compose.yml
```
web:
  image: 'gitlab/gitlab-ce:latest'
  restart: always
  hostname: 'gitlab.anhao.cn'
  environment:
#    GITLAB_TIMEZONE: PRC
#    GITLAB_ROOT_PASSWORD: wangnan123
#    SMTP_ENABLE: true
#    GITLAB_EMAIL_FROM: jadesouth@aliyun.com
#    GITLAB_EMAIL_DISPLAY_NAME: jadesouth
#    GITLAB_EMAIL_REPLY_TO: jadesouth@aliyun.com
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://gitlab.anhao.cn:8000'
      gitlab_rails['gitlab_shell_ssh_port'] = 2200
  ports:
    - "8000:80"
#    - "4433:443"
    - "2200:22"
  volumes:
    - '/data/gitlab/config:/etc/gitlab'
    - '/data/gitlab/log:/var/log/gitlab'
    - '/data/gitlab/data:/var/opt/gitlab'
```
## 2. 启动
cd docker-composer.yml所在目录
docker-compose up -d


# 目前用的这个
```
docker run --detach \
    --hostname gitlab.anhao.cn \
    --publish 0.0.0.0:8000:8000 \  // 容器内部也映射到8000，是为了发邮件的时候连接地址也带8000端口，同时需要修改配置文件
    --publish 0.0.0.0:2200:22 \
    --name gitlab \
    --restart always \
    --volume /data/gitlab/config:/etc/gitlab \
    --volume /data/gitlab/logs:/var/log/gitlab \
    --volume /data/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```
```
docker exec -it gitlab /bin/bash
```
| `/srv/gitlab/data` | `/var/opt/gitlab` | For storing application data |
| `/srv/gitlab/logs` | `/var/log/gitlab` | For storing logs |
| `/srv/gitlab/config` | `/etc/gitlab` | For storing the GitLab configuration files |

关闭后

