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

##### SECURITY安全

| 配置参数   | 默认值   | 说明                                                         |
| ---------- | -------- | ------------------------------------------------------------ |
| requirepas | foobared | 访问密码的查看、设置和取消。默认是foobared，代表不需要认证，如果需要的话，改为自己的password |

##### LIMITS限制

| 配置参数          | 默认值         | 说明                                                         |
| ----------------- | -------------- | ------------------------------------------------------------ |
| maxclients        | 10000          | 设置redis同时可以与多少个客户端进行连接                      |
| maxmemory         | 机器的最大内存 | 设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，<br/>那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等 |
| maxmemory-policy  | noeviction     | 设置当redis达到最大内存是应通过怎样的方式删除内容<br/>volatile-lru：使用lru算法移除key，只对设置了过期时间的key<br/>allkeys-lru：使用lru算法移除key<br/>volatile-random：在过期集合中随机的移除key，只对设置了过期时间的key<br/>allkeys-random：移除随机的key<br/>volatile-ttl：移除哪些ttl最小的key，即最近要过期的key<br/>noevivtion：不进行移除，针对写操作，只返回错误信息 |
| maxmemory-samples | 5              | 设置过期策略选取的样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，<br/>redis默认会检查这么多个key并选择其中LRU的那个 |

##### APPEND ONLY MODE追加

| 配置参数                    | 默认值         | 说明                                                         |
| --------------------------- | -------------- | ------------------------------------------------------------ |
| appendonly                  | no             | 默认是关，即不开启。yes的话代表启动aof来备份，启动时使用aof来恢复以及修复 |
| appendfilename              | appendonly.aof | 使用aof持久化数据的文件名称                                  |
| appendsync                  | everysec       | aof持久化方式<br/>always：同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好<br/>everysec：出厂默认推荐，异步操作，每秒记录   如果一秒内宕机，有数据丢失<br/>no：不启用 |
| no-appendfsync-on-rewrite   | no             | 重写时是否可以运用Appendfsync，用默认no即可，保证数据安全性。 |
| auto-aof-rewrite-min-size   | 100            | 设置重写的百分比，达到设定内存的百分之多少时进行重写         |
| auto-aof-rewrite-percentage | 64mb           | 触发重写机制的aof的内存大小                                  |

##### 常见配置Redis.conf介绍

1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    daemonize no
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid
3. 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    port 6379
4. 绑定的主机地址
    bind 127.0.0.1
5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    timeout 300
6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
    loglevel verbose
7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
    logfile stdout
8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    databases 16
9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    save <seconds> <changes>
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    rdbcompression yes
11. 指定本地数据库文件名，默认值为dump.rdb
    dbfilename dump.rdb
12. 指定本地数据库存放目录
    dir ./
13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    slaveof <masterip> <masterport>
14. 当master服务设置了密码保护时，slav服务连接master的密码
    masterauth <master-password>
15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    requirepass foobared
16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxclients 128
17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    maxmemory <bytes>
18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly no
19. 指定更新日志文件名，默认为appendonly.aof
      appendfilename appendonly.aof
20. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折衷，默认值）
    appendfsync everysec
21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
      vm-enabled no
22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
      vm-swap-file /tmp/redis.swap
23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
      vm-max-memory 0
24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
      vm-page-size 32
25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
      vm-pages 134217728
26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
      vm-max-threads 4
27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    glueoutputbuf yes
28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    hash-max-zipmap-entries 64
    hash-max-zipmap-value 512
29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
    activerehashing yes
30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    include /path/to/local.conf

### Redis的基本数据类型

