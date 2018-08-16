Redis数据迁移
===

思路：做主从，将新机器做为被迁移机器的从库，待数据全部复制到从库后，关闭主服务器，设置从库为主库。
https://yq.aliyun.com/articles/62511


## 1. 设置从库   @y(l6?Nq%dAQ<*+k
```
从库配置文件添加:
slaveof 112.124.112.128 63799

slaveof 114.215.196.126 6379

或
从库客户端执行命令:
slaveof 112.124.112.128 63799

slaveof 114.215.196.126 6379


如果master通过requirepass设置了密码:
配置从库:
masterauth <password>
或
从库命令行执行:
config set masterauth <password>

config set masterauth kele

```
## 2. 数据迁移
设置完成后就会进行数据同步，最好查看日志看是否有错误。

## 3. 迁移完成
取消主从设置，从库升级为主库
```
slaveof no one
```

## 4. 善后
### 确保旧的服务器的服务已经停止服务

上面没有配合说明业务代码对服务器的请求切换，但是我们在切换完服务之后肯定需要转移业务代码的请求吧。那么如何确定服务已经完全转移走了呢？

在旧服务器上通过`netstat`命令查看是否还有请求过来。

```none
$ netstat -an|grep "100:6379"|wc -l
```

有时候`netstat`的结果也不一定准，因为有些请求已经不在，但是`socket`状态还在，比如`CLOSE_WAIT`状态最长可持续2小时。同时`socket`请求太快，也会出现`netstat`没数据，但是实际网卡有流量的情况。

我们可以通过`tcdump`监控网卡在该端口上确实已经没有流量。

```none
tcpdump  -i em2 -vv -nn host  192.168.1.100 and  port 6379
```

注意排除监控系统对该`redis`实例的请求。

### 如何判断 redis 已经同步完毕呢?

1.  在命令行查看 info `master_link_status:up`，则表示同步完成了。

2.  日志文件也可以看

    ```
    [48864] 12 Jun 11:24:03.549 * The server is now ready to accept connections on port 6310
    [48864] 12 Jun 11:31:52.936 * SLAVE OF 192.168.50.17:8004 enabled (user request)
    [48864] 12 Jun 11:31:53.467 * Connecting to MASTER 192.168.50.17:8004
    [48864] 12 Jun 11:31:53.467 * MASTER <-> SLAVE sync started
    [48864] 12 Jun 11:31:53.470 * Non blocking connect for SYNC fired the event.
    [48864] 12 Jun 11:31:53.470 * Master replied to PING, replication can continue...
    [48864] 12 Jun 11:31:53.470 * Partial resynchronization not possible (no cached master)
    [48864] 12 Jun 11:31:53.470 * Master does not support PSYNC or is in error state (reply: -ERR unknown command 'PSYNC')
    [48864] 12 Jun 11:31:53.470 * Retrying with SYNC...
    [48864] 12 Jun 11:32:13.085 * MASTER <-> SLAVE sync: receiving 484322714 bytes from master
    [48864] 12 Jun 11:32:18.498 * MASTER <-> SLAVE sync: Flushing old data
    [48864] 12 Jun 11:32:18.498 * MASTER <-> SLAVE sync: Loading DB in memory
    [48864] 12 Jun 11:32:32.349 * MASTER <-> SLAVE sync: Finished with succes
    ```


## 备注
老服务器上内存一直报警，所以要把一部分`redis`数据迁移到新服务器上去。

迁移的方式有两种，一种是停服务器，搬迁数据；另一种通过主从同步转移。

## 停服务器，搬迁数据

1.  首先在原服务器上执行`redis-cli shutdown`命令，该命令会触发保证写`RDB`文件以及将`AOF`文件写入磁盘，不会丢失数据。 如果是`kill -9 pid`就会丢失数据。

2.  然后将`RDB`文件和`AOF`文件都拷贝到新服务器上，注意需要与`redis.conf`文件中指定`RDB`文件名和`AOF`文件名匹配。

3.  最后在新服务器上启动`redis`服务器。

个人觉得这种方式有点暴力，因为中间将数据保存到磁盘、拷贝数据、启动服务器，将数据从磁盘导入内存，会有很长一段时间。这段时间对线上服务造成不小的影响，所以我也没这么用。

## 主从同步转移

1.  首先在新服务器上直接进入`redis-cli`，执行从库配置`slaveof 192.168.1.100 6379`，这里假设要将`192.168.1.100`的`6379`端口的`redis`服务转移过来。这样就已经开始同步了。通过`info`可以查看当前服务器是`slave`。

2.  然后通过`info`命令查看`master_link_status`，如果为`up`，表示同步完成。（在同步过程中，执行查询的时候还是会提示"Redis is loading the dataset in memory"，这属于正常情况.把数据从磁盘文件加载到内存中可能会消耗很长的一段时间。）

3.  最后断开主从关系，在`redis-cli`命令行下执行`slaveof no one`提示`OK`，再通过`info`查看，该新服务器已经自己变成`master`了。

## 善后

### 确保旧的服务器的服务已经停止服务

上面没有配合说明业务代码对服务器的请求切换，但是我们在切换完服务之后肯定需要转移业务代码的请求吧。那么如何确定服务已经完全转移走了呢？

在旧服务器上通过`netstat`命令查看是否还有请求过来。

```none
$ netstat -an|grep "100:6379"|wc -l
```

有时候`netstat`的结果也不一定准，因为有些请求已经不在，但是`socket`状态还在，比如`CLOSE_WAIT`状态最长可持续2小时。同时`socket`请求太快，也会出现`netstat`没数据，但是实际网卡有流量的情况。

我们可以通过`tcdump`监控网卡在该端口上确实已经没有流量。

```none
tcpdump  -i em2 -vv -nn host  192.168.1.100 and  port 6379
```

注意排除监控系统对该`redis`实例的请求。

### 如何判断 redis 已经同步完毕呢?

1.  在命令行查看 info `master_link_status:up`，则表示同步完成了。

2.  日志文件也可以看

    ```none
    [48864] 12 Jun 11:24:03.549 * The server is now ready to accept connections on port 6310
    [48864] 12 Jun 11:31:52.936 * SLAVE OF 192.168.50.17:8004 enabled (user request)
    [48864] 12 Jun 11:31:53.467 * Connecting to MASTER 192.168.50.17:8004
    [48864] 12 Jun 11:31:53.467 * MASTER <-> SLAVE sync started
    [48864] 12 Jun 11:31:53.470 * Non blocking connect for SYNC fired the event.
    [48864] 12 Jun 11:31:53.470 * Master replied to PING, replication can continue...
    [48864] 12 Jun 11:31:53.470 * Partial resynchronization not possible (no cached master)
    [48864] 12 Jun 11:31:53.470 * Master does not support PSYNC or is in error state (reply: -ERR unknown command 'PSYNC')
    [48864] 12 Jun 11:31:53.470 * Retrying with SYNC...
    [48864] 12 Jun 11:32:13.085 * MASTER <-> SLAVE sync: receiving 484322714 bytes from master
    [48864] 12 Jun 11:32:18.498 * MASTER <-> SLAVE sync: Flushing old data
    [48864] 12 Jun 11:32:18.498 * MASTER <-> SLAVE sync: Loading DB in memory
    [48864] 12 Jun 11:32:32.349 * MASTER <-> SLAVE sync: Finished with succes
    ```
