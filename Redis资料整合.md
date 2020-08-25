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

​		  ① redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何。

​		  ② redis-check-aof：修复有问题的AOF文件。

​          ③ redis-check-dump：修复有问题的dump.rdb文件。

​          ④ redis-cli：客户端，操作入口。

​          ⑤ redis-sentinel：redis集群使用。

​          ⑥ redis-server：Redis服务器启动命令。

​	7、启动

​		  ① redis-server /myredis/redis.conf  (运行指定配置文件的redis服务)

​          ② redis-cli -p 6379 （启动客户端）

### Redis配置文件详解

------

#####    GENERAL通用

###### 	daemonize

​     是否以守护进程方式运行。默认为no。

###### 	pidfile

​	当以守护进程方式运行时，默认在此路径<u>/var/run/redis.pid</u>下有个pid，应该是进程的pid

###### 	port

​	默认提供服务的端口号。默认是6379.

###### 	tcp-backlog

​			是一个连接队列，是三次握手成功与未完成三次握手队列的总和。高并发情况下需要设置一个高backlog值	来避免慢客户端连接的值。需要注意的是，linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值，	所以需要增大somaxconn和tcp_max_sync_backlog两个值.

   		 默认是511.

###### 	timeout

​	当客户端闲置多少时间关闭该连接，默认是0，0的话代表关闭该功能。

###### 	tcp-keepalive

​	当多台reids做主从或者集群的时候，心跳的时间。默认是0，代表不检测。

###### 	loglevel

​	Redis日志的级别。

​    默认为verbose：

​					① debug    很多的信息，在开发测试阶段有用。

​					② verbose  冗长的，啰嗦的，比debug的日志稍微少一点，更加有用。

​					③ notice      用于生产环境，更多你关注的信息。

​					④ warning   非常重要的信息。

###### logfile

​	一个标准的输出，如果以daemon线程启动的话，被发送到/dev/null下。默认为 “ ”。

###### syslog-enable

​	系统日志。默认是no，关闭。

###### syslog-ident

​	指定系统日志身份。如果系统日志开启的话，默认以redis开头。

###### syslog-facility

​	输出日志的设备，值可以是user或local0-local7。默认是local0。

###### database

Redis的数据库数量。默认是16。

##### SNAPSHOTTING快照

###### save

​	进行快照存储的次数，不开启需要设置为“”。默认是1分钟10000次，5分钟10次，15分钟1次。

###### stop-writes-on-bgsave-error

​	在回头存储的时候，停止写入。不停止的话会有数据一致性的问题。

###### rdbcompression

​	是否开启缓存压缩。是的话，会使用LZF算法来进行压缩，默认是yes。

###### rdbchecksum

​	是否使用crc算法进行数据校验，默认是yes。

###### dbfilename	

​	存储的rdb文件的名字，默认是“dump.rdb"。

###### dir

​	本地数据库的存放目录，默认是./。

##### REPLICATION复制

###### 

