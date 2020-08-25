### Redis基本概念

------

##### Redis是什么

​	1、REmote DIctionary Server(远程字典服务器)。

​	2、是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的（Key/Value）分布式内存数据库，基		  于内存运行并支持数据持久化的NoSql数据库，是当前最热门的NoSql数据库之一，也被人们称为数据结构            	      服务器。

##### Redis的特点

​	1、Redis支持数据的持久化，而已将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。

​	2、Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。

​	3、Redis支持数据的备份，即master-slave模式的数据备份。

​	4、支持部分事务操作。

##### Redis安装

`注：企业里面做Redis开发，99%都是Linux版的运用和安装，几乎不会涉及到Windows版。`

​	1、下载获得Redis-3.0.4.tar.gz后将它放入linux目录/opt。（两种方式：①在指定目录下使用wget http://down		   load.redis.io/releases/redis-3.0.4.tar.gz ②进入官网下载好使用ftp上传的linux上）

​	2、/opt目录下，解压命令:tar -zxvf redis-3.0.4.tar.gz。

​	3、解压完成后出现文件夹：redis-3.0.4。进入目录：cd redis-3.0.4。

​	4、在redis-3.0.4目录下执行make命令。

​	5、如果make完成后继续执行make install。

​	6、查看默认安装目录：usr/local/bin。

​          目录下文件说明：

```
redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何。
redis-check-aof：修复有问题的AOF文件。
redis-check-dump：修复有问题的dump.rdb文件。
redis-cli：客户端，操作入口。
redis-sentinel：redis集群使用。
redis-server：Redis服务器启动命令。
```

​	7、启动

```
redis-server /myredis/redis.conf  (运行指定配置文件的redis服务)
redis-cli -p 6379 （启动客户端）
```



### Redis配置文件详解

------

#####    GENERAL通用



| 配置参数        | 默认值             | 说明                                                         |
| --------------- | ------------------ | ------------------------------------------------------------ |
| daemonize       | no                 | 是否以守护进程方式运行                                       |
| pidfile         | /var/run/redis.pid | 当以守护进程方式运行时，默认在此路径<u>/var/run/redis.pid</u>下有个pid，应该是进程的pid |
| port            | 6379               | 默认提供服务的端口号                                         |
| tcp-backlog     | 511                | 是一个连接队列，是三次握手成功与未完成三次握手队列的总和。高并发情况下需要设置一个高backlog值	来避免慢客户端连接的值。需要注意的是，linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值，	所以需要增大somaxconn和tcp_max_sync_backlog两个值. |
| timeout         | 0                  | 当客户端闲置多少时间关闭该连接，0的话代表关闭该功能          |
| tcp-keepalive   | 0                  | 当多台reids做主从或者集群的时候，心跳的时间，0代表不检测     |
| loglevel        | notice             | debug    很多的信息，在开发测试阶段有用。<br/>verbose  冗长的，啰嗦的，比debug的日志稍微少一点，更加有用。notice   用于生产环境，更多你关注的信息。<br/>warning  非常重要的信息。 |
| logfile         | “ ”                | 一个标准的输出，如果以daemon线程启动的话，被发送到/dev/null下 |
| syslog-enable   | no                 | 系统日志，默认关闭                                           |
| syslog-ident    | redis              | 指定系统日志身份。如果系统日志开启的话，默认以redis开头。    |
| syslog-facility | local0             | 输出日志的设备，值可以是user或local0-local7                  |
| database        | 16                 | Redis的数据库数量                                            |

##### SNAPSHOTTING快照



| 配置参数                    | 默认值                             | 说明                                                       |
| --------------------------- | ---------------------------------- | ---------------------------------------------------------- |
| save                        | 1分钟10000次，5分钟10次，15分钟1次 | 进行快照存储的次数，不开启需要设置为“ ”                    |
| stop-writes-on-bgsave-error | yes                                | 在回头存储的时候，停止写入。不停止的话会有数据一致性的问题 |
| rdbcompression              | yes                                | 是否开启缓存压缩。是的话，会使用LZF算法来进行压缩          |
| rdbchecksum                 | yes                                | 是否使用crc算法进行数据校验                                |
| dbfilename                  | dump.rdb                           | 存储的rdb文件的名字                                        |
| dir                         | ./                                 | 本地数据库的存放目录                                       |

##### REPLICATION复制



| 配置参数                        | 默认值 | 说明                                                         |
| ------------------------------- | ------ | ------------------------------------------------------------ |
| slaveof <masterip> <masterport> | 无     | 设置当前redis从库的主库ip和端口，与命令不同的是，配置文件的设置redis每次启动都会读取配置进行重新设置，而命令设置主从，重新启动后原来的主从库将没有关系 |
| masterauth  <master-password>   | 无     | 如果主服务器设置了密码，从服务器在开始同步前需要做一个密码的认证 |
| slave-serve-stale-data          | yes    | yes代表从服务器与主服务器断开连接的话，仍能提供服务，no代表会返回一个错误信息 |
| slave-read-only                 | yes    | 从服务器只能读不能写                                         |
| repl-diskless-sync              | no     | 是否开启无磁盘同步（rdb写文件到磁盘时会传输一份给slave，或者直接从内存中写数据到从服务器的socket） |
| repl-diskless-sync-delay        | 5      | 如果开启无磁盘同步的话，延时的时间是多少秒                   |
| repl-ping-slave-period          | 10     | 主服务器发送ping给从服务器的时间，也就是心跳时间是多少秒     |
| repl-timeout                    | 60     | 主从服务器的超时时间                                         |
| repl-disable-tcp-nodelay        | no     |                                                              |
| repl-backlog-size               | 1mb    | 复制的backlog的大小，对于一个已经 同步的从服务器失去连接一段时间，再次连接的话，会从主服务器的这个缓存中进行复制失去连接的服务器的内容，而不需要进行全量的复制 |
| repl-backlog-ttl                | 3600   | 主服务器长时间不连接这个从服务器，backlog的失效时间          |
| slave-priority                  | 100    | 哨兵模式下，从服务器成为主服务器的优先级                     |
| min-slaves-to-write             | 3      | 当主服务器关联的从服务器少于设定值时，主服务器就会停止写入   |
| min-slaves-max-lag              | 10     | 从服务器的最大连接时间少于设定值时，主服务器就会停止写入     |

