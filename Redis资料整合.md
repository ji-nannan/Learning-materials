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

------

Redis支持5种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

##### string

​		string是 redis 最基本的类型，你可以理解成与 Memcached 一模一样的类型，一个 key 对应一个 value。value其实不仅是String，也可以是数字。string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。

常用命令：get（读）、set（写）、incr（增）、decr（减）、mget（批量读）等。

应用场景：

​		String是最常用的一种数据类型，普通的key/ value 存储都可以归为此类，即可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受Redis的定时持久化，操作日志及 Replication等功能。除了提供与 Memcached 一样的get、set、incr、decr 等操作外，Redis还提供了下面一些操作：

- 获取字符串长度
- 往字符串append内容
- 设置和获取字符串的某一段内容
- 设置及获取字符串的某一位（bit）
- 批量设置一系列字符串的内容

使用场景：常规key-value缓存应用。常规计数: 微博数, 粉丝数。

实现方式：

​		String在redis内部存储默认就是一个字符串，被redisObject所引用，当遇到incr,decr等操作时会转成数值型进行计算，此时redisObject的encoding字段为int。

##### hash

​		Redis hash是一个键值(key => value)对集合。Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

常用命令：hget（读）,hset（写）,hgetall（批量读） 等。

应用场景：我们简单举个实例来描述下Hash的应用场景，比如我们要存储一个用户信息对象数据，包含以下信息：

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821202451984-479691318.png)

​		第一种方式将用户ID作为查找key,把其他信息封装成一个对象以序列化的方式存储，这种方式的缺点是，增加了序列化/反序列化的开销，并且在需要修改其中一项信息时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入CAS等复杂问题。

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821202546802-636845502.png)

​		第二种方法是这个用户信息对象有多少成员就存成多少个key-value对儿，用用户ID+对应属性的名称作为唯一标识来取得对应属性的值，虽然省去了序列化开销和并发问题，但是用户ID为重复存储，如果存在大量这样的数据，内存浪费还是非常可观的。

​		那么Redis提供的Hash很好的解决了这个问题，Redis的Hash实际是内部存储的Value为一个HashMap，并提供了直接存取这个Map成员的接口，如下图：

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821202632663-939669694.png)

​		也就是说，Key仍然是用户ID, value是一个Map，这个Map的key是成员的属性名，value是属性值，这样对数据的修改和存取都可以直接通过其内部Map的Key(Redis里称内部Map的key为field), 也就是通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题，很好的解决了问题。

​		这里同时需要注意，Redis提供了接口(hgetall)可以直接取到全部的属性数据，但是如果内部Map的成员很多，那么涉及到遍历整个内部Map的操作，由于Redis单线程模型的缘故，这个遍历操作可能会比较耗时，而另其它客户端的请求完全不响应，这点需要格外注意。

使用场景：存储部分变更数据，如用户信息等。

实现方式：

​		上面已经说到Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现，这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap，当成员数量增大时会自动转成真正的HashMap，此时encoding为ht。

##### list

​		Redis list列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

常用命令：

​		lpush（添加左边元素）,rpush,lpop（移除左边第一个元素）,rpop,lrange（获取列表片段，LRANGE key start stop）等。

应用场景：

​		Redis list的应用场景非常多，也是Redis最重要的数据结构之一，比如微博的关注列表，粉丝列表等都可以用Redis的list结构来实现。

​		使用List结构，我们可以轻松地实现最新消息排行等功能。List的另一个应用就是消息队列，实现方式：Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，Redis内部的很多实现，包括发送缓冲队列等也都是用的这个数据结构。

​		Redis的list是每个子元素都是String类型的双向链表，可以通过push和pop操作从列表的头部或者尾部添加或者删除元素，这样List即可以作为栈，也可以作为队列。 获取越接近两端的元素速度越快，但通过索引访问时会比较慢。可以利用List的PUSH操作，将任务存在List中，然后工作线程再用POP操作将任务取出进行执行。Redis还提供了操作List中某一段的api，你可以直接查询，删除List中某一段的元素。

使用场景：消息队列系统：使用list可以构建队列系统，使用sorted set甚至可以构建有优先级的队列系统。

##### set

​		Redis Set是string类型的无序集合。集合是通过hashtable实现的，概念和数学中个的集合基本类似，可以交集，并集，差集等等，set中的元素是没有顺序的。所以添加，删除，查找的复杂度都是O(1)。

sadd命令：添加一个 string 元素到 key 对应的 set 集合中，成功返回1，如果元素已经在集合中返回 0，如果 key 对应的 set 不存在则返回错误。

常用命令：sadd,spop,smembers,sunion 等。

应用场景：

​		Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。

　　Set 就是一个集合，集合的概念就是一堆不重复值的组合。利用Redis提供的Set数据结构，可以存储一些集合性的数据。

　　案例：在微博中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis还为集合提供了求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能，对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。

　　实现方式： set 的内部实现是一个 value永远为null的HashMap，实际就是通过计算hash的方式来快速排重的，这也是set能提供判断一个成员是否在集合内的原因。

使用场景：

​	①交集，并集，差集：(Set)

​	②获取某段时间所有数据去重值

##### zset

Redis zset和 set 一样也是string类型元素的集合,且不允许重复的成员。

zadd 命令：添加元素到集合，元素在集合中存在则更新对应score。

常用命令：

​		zadd,zrange,zrem,zcard等使用场景：Redis sorted set的使用场景与set类似，区别是set不是自动有序的，而sorted set可以通过用户额外提供一个优先级(score)的参数来为成员排序，并且是插入有序的，即自动排序。当你需要一个有序的并且不重复的集合列表，那么可以选择sorted set数据结构，比如twitter 的public timeline可以以发表时间作为score来存储，这样获取时就是自动按时间排好序的。和Set相比，**Sorted Set关联了一个double类型权重参数score**，使得集合中的元素能够按score进行有序排列，redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。比如一个存储全班同学成绩的Sorted Set，其集合value可以是同学的学号，而score就可以是其考试得分，这样在数据插入集合的时候，就已经进行了天然的排序。另外还可以用Sorted Set来做带权重的队列，比如普通消息的score为1，重要消息的score为2，然后工作线程可以选择按score的倒序来获取工作任务。让重要的任务优先执行。

实现方式：

​		Redis sorted set的内部使用HashMap和跳跃表(SkipList)来保证数据的存储和有序，HashMap里放的是成员到score的映射，而跳跃表里存放的是所有的成员，排序依据是HashMap里存的score,使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。

### Redis的持久化操作

------

#### RDB

###### 基本概念

​		在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里。

​		Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能，如果需要进行大规模的数据恢复，且对数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能会丢失。

​		RDB 保存的是dump.rdb文件。

###### Fork

​		fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）
数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。

###### 如何触发RDB快照

​		配置文件中的save操作。默认为1分钟10000次，5分钟10次，15分钟1次。

​		save时只管保存，其它不管，全部阻塞。

​		bgsave时Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。可以通过lastsave命令获取最后一次成功执行快照的时间。

​		执行flushall（清空数据）命令，也会产生dump.rdb文件，但里面是空的，无意义。

###### 如何恢复

​		将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可。使用config get dir可获取目录。

###### 优势

1. 适合大规模的数据恢复。
2. 对数据完整性和一致性要求不高。

###### 劣势

1. 在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改。
2. fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑。

###### 如何停止

1. 将配置文件中默认的save操作改为save “ ”。
2. 动态停止所有RDB保存规则的方法：redis-cli config set save " "。

#### AOF

###### 基本概念

​		以日志形式来记录每个操作，将Redis执行过的所有**写**指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

​		Aof保存的是appendonly.aof文件。

###### 启动

​		修改配置文件中默认的appendonly no，改为yes。

###### 正常恢复数据

1. 将有数据的aof文件复制一份保存到对应目录(config get dir)。
2. 重启redis然后重新加载。

###### 异常恢复数据

1. 备份被写坏的AOF文件。
2. 使用Redis安装目录下的redis-check-aof --fix工具进行修复。（上文提到过）
3. 重启redis然后重新加载

###### rewrite（重写）

概念：

​		AOF采用文件追加方式，文件会越来越大，为避免出现此种情况，新增了重写机制，当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集.可以使用命令bgrewriteaof。 

重写原理：

​		AOF文件持续增长而文件过大时，会fork出一条新进程来将文件重写（也是先写临时文件再rename），遍历新进程的内存中数据，每条记录有一条的Set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。

触发机制：

​		Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。

###### 优势

1. 每修改同步：appendfsync always   同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好。
2. 每秒同步：appendfsync everysec    异步操作，每秒记录   如果一秒内宕机，有数据丢失。
3. 不同步：appendfsync no   从不同步。

###### 劣势

1. 相同数据集的数据而言AOF文件要远大于RDB文件，恢复速度慢于rdb。
2. AOF运行效率要慢于RDB,每秒同步策略效率较好，不同步效率和rdb相同。

#### RDB和AOF持久化总结

​		RDB：RDB 持久化机制，是对 redis 中的数据执行周期性的持久化。

​		AOF：AOF 机制对每条写入命令作为日志，以 append-only 的模式写入一个日志文件中，在 redis重启的时候，可以通过回放 AOF 日志中的写入指令来重新构建整个数据集。

​		通过 RDB 或 AOF，都可以将 redis 内存中的数据给持久化到磁盘上面来，然后可以将这些数据备份到别的地方去，比如说阿里云等云服务。

​		如果 redis 挂了，服务器上的内存和磁盘上的数据都丢了，可以从云服务上拷贝回来之前的数据，放到指定的目录中，然后重新启动 redis，redis 就会自动根据持久化数据文件中的数据，去恢复内存中的数据，继续对外提供服务。

​		如果同时使用 RDB 和 AOF 两种持久化机制，那么在 redis 重启的时候，会使用 AOF 来重新构建数据，因为 AOF 中的数据更加完整。

###### RDB优缺点

1. RDB 会生成多个数据文件，每个数据文件都代表了某一个时刻中 redis 的数据，这种多个数据文件的方式，非常适合做冷备，可以将这种完整的数据文件发送到一些远程的安全存储上去，比如说 Amazon的 S3 云服务上去，在国内可以是阿里云的 ODPS 分布式存储上，以预定好的备份策略来定期备份 redis中的数据。
2. RDB 对 redis 对外提供的读写服务，影响非常小，可以让 redis 保持高性能，因为 redis 主进程只需要 fork 一个子进程，让子进程执行磁盘 IO 操作来进行 RDB 持久化即可。
3. 相对于 AOF 持久化机制来说，直接基于 RDB 数据文件来重启和恢复 redis 进程，更加快速
4. 如果想要在 redis 故障时，尽可能少的丢失数据，那么 RDB 没有 AOF 好。一般来说，RDB 数据快照文件，都是每隔 5 分钟，或者更长时间生成一次，这个时候就得接受一旦 redis 进程宕机，那么会丢失最近 5 分钟的数据。
5. RDB 每次在 fork 子进程来执行 RDB 快照数据文件生成的时候，如果数据文件特别大，可能会导致对客户端提供的服务暂停数毫秒，或者甚至数秒。

###### AOF优缺点

1. AOF 可以更好的保护数据不丢失，一般 AOF 会每隔 1 秒，通过一个后台线程执行一次 fsync 操作，最多丢失 1 秒钟的数据。
2. AOF 日志文件以 append-only 模式写入，所以没有任何磁盘寻址的开销，写入性能非常高，而且文件不容易破损，即使文件尾部破损，也很容易修复。
3. AOF 日志文件即使过大的时候，出现后台重写操作，也不会影响客户端的读写。因为在 rewrite log的时候，会对其中的指令进行压缩，创建出一份需要恢复数据的最小日志出来。在创建新日志文件的时候，老的日志文件还是照常写入。当新的merge后日志文件ready的时候，在交换新老日志文件即可。
4. AOF 日志文件的命令通过非常可读的方式进行记录，这个特性非常适合做灾难性的误删除的紧急恢复。比如某人不小心用 flushall 命令清空了所有数据，只要这个时候后台 rewrite 还没有发生，那么就可以立即拷贝 AOF 文件，将最后一条 flushall 命令给删了，然后再将该 AOF 文件放回去，就可以通过恢复机制，自动恢复所有数据。
5. 对于同一份数据来说，AOF 日志文件通常比 RDB 数据快照文件更大。
6. AOF 开启后，支持的写 QPS 会比 RDB 支持的写 QPS 低，因为 AOF 一般会配置成每秒 fsync 一次日志文件，当然，每秒一次 fsync，性能也还是很高的。（如果实时写入，那么 QPS 会大降，redis 性 能会大大降低）
7. 以前 AOF 发生过 bug，就是通过 AOF 记录的日志，进行数据恢复的时候，没有恢复一模一样的数据出来。所以说，类似 AOF 这种较为复杂的基于命令日志 / merge / 回放的方式，比基于 RDB 每次持久化一份完整的数据快照文件的方式，更加脆弱一些，容易有 bug。不过 AOF 就是为了避免 rewrite 过程导致的 bug，因此每次 rewrite 并不是基于旧的指令日志进行 merge 的，而是基于当时内存中的数据进行指令的重新构建，这样健壮性会好很多。

###### RDB 和 AOF 到底该如何选择

不要仅仅使用 RDB，因为那样会导致你丢失很多数据；

也不要仅仅使用 AOF，因为那样有两个问题：第一，你通过 AOF 做冷备，没有 RDB 做冷备来的恢复速度更快；第二，RDB 每次简单粗暴生成数据快照，更加健壮，可以避免 AOF 这种复杂的备份和恢复机制的 bug；