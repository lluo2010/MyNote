# Redis study note


## 概述

---

Redis是一个开源的，基于内存的结构化数据存储媒介，可以作为数据库、缓存服务或消息服务使用, 它是一个键-值 NoSQL 数据存储解决方案。

Redis支持多种数据结构，包括字符串、哈希表、链表、集合、有序集合、位图、Hyperloglogs等。

Redis具备LRU淘汰、事务实现、以及不同级别的硬盘持久化等能力，并且支持副本集和通过Redis Sentinel实现的高可用方案，同时还支持通过Redis Cluster实现的数据自动分片能力。

Redis 是一种内存型数据存储，也可以将它写入磁盘中来实现耐久性。Redis 可通过两种方式来持久存储数据：RDB 和 AOF。RDB 持久性按照指定的间隔对您的数据集执行时间点快照。它不是非常耐久，而且您可能会丢失一些数据，但它非常快。AOF 的持久性要长得多，而且记录了服务器收到的每个写入操作。这些写入操作在服务器启动时重新执行，重新构建原始数据集。在查询 Redis 时，将从内存中获取数据，绝不会从磁盘获取数据，Redis 对内存中存储的键和值执行所有操作。

Redis 采用了一种客户端/服务器模型，借用该模型来监听 TCP 端口并接受命令。Redis 中的所有命令都是原子性的，所以您可以从不同的客户端处理同一个键，没有任何争用条件。如果您使用的是 memcached（一个内存型对象缓存系统），您会发现自己对它很熟悉，但 Redis（可以说）是 memcached++。Redis 也支持数据复制。

Redis是一种高级的key:value存储系统，其中value支持五种数据类型：

1. 字符串（strings）
1. 字符串列表（lists）
1. 字符串集合（sets）
1. 有序字符串集合（sorted sets）
1. 哈希（hashes）

而关于key，有几个点要提醒大家：

1. key不要太长，尽量不要超过1024字节，这不仅消耗内存，而且会降低查找的效率；
1. key也不要太短，太短的话，key的可读性会降低；
1. 在一个项目中，key最好使用统一的命名模式，例如user:10000:passwd。


Redis的主要功能都基于单线程模型实现，也就是说Redis使用一个线程来服务所有的客户端请求，同时Redis采用了非阻塞式IO，并精细地优化各种命令的算法时间复杂度，这些信息意味着：

1. Redis是线程安全的（因为只有一个线程），其所有操作都是原子的，不会因并发产生数据异常.
1. Redis的速度非常快（因为使用非阻塞式IO，且大部分命令的算法时间复杂度都是O(1)).
1. 使用高耗时的Redis命令是很危险的，会占用唯一的一个线程的大量处理时间，导致所有的请求都被拖慢。（例如时间复杂度为O(N)的KEYS命令，严格禁止在生产环境中使用）.


关键优势:

1. Redis 的优势包括它的速度、它对富数据类型的支持、它的操作的原子性，以及它的通用性：
1. Redis 非常快。它每秒可执行约 100,000 个 SET 以及约 100,000 个 GET 操作。您可以使用 redis-benchmark 实用程序在自己的机器上对它的性能进行基准测试。（redis-benchmark 模拟在它发送总共 M 个查询的同时，N 个客户端完成的 1. SET/GET 操作。）
1. Redis 对大多数开发人员已知道的大多数数据类型提供了原生支持，这使得各种问题得以轻松解决。经验会告诉您哪个问题最好由何种数据类型来处理。
1. 因为所有 Redis 操作都是原子性的，所以多个客户端会并发地访问一个 Redis 服务器，获取相同的更新值。
1. Redis 是一个多效用工具，对许多用例很有用，这些用例包括缓存、消息队列（Redis 原生支持发布/订阅）、短期应用程序数据（比如 Web 会话、Web 页面命中计数）等。


## xxx

todo...XXX



## 对Redis数据库做下小结

---

1. 提高了DB的可扩展性，只需要将新加的数据放到新加的服务器上就可以了 
1. 提高了DB的可用性，只影响到需要访问的shard服务器上的数据的用户 
1. 提高了DB的可维护性，对系统的升级和配置可以按shard一个个来搞，对服务产生的影响较小 
1. 小的数据库存的查询压力小，查询更快，性能更好

 

## Redis与Memcached的比较

---

1. Memcached是多线程，而Redis使用单线程.
1. Memcached使用预分配的内存池的方式，Redis使用现场申请内存的方式来存储数据，并且可以配置虚拟内存。
1. Redis可以实现持久化，主从复制，实现故障恢复。
1. Memcached只是简单的key与value,但是Redis支持数据类型比较多。



## Reference

---

1. [Redis Documentation](https://redis.io/documentation)
1. [Java 开发 2.0: 现实世界中的 Redis](https://www.ibm.com/developerworks/cn/java/j-javadev2-22/)
1. [开发 Spring Redis 应用程序](https://www.ibm.com/developerworks/cn/java/os-springredis/)
1. [超强、超详细Redis数据库入门教程***](http://www.jianshu.com/p/7e22ad3a9061)
1. [Redis工作系列之一 与 Memcached对比理解**](http://blog.csdn.net/lishehe/article/details/45601423)
1. [Redis基础、高级特性与性能调优](http://www.jianshu.com/p/2f14bc570563)
1. [分布式缓存技术PK：选择Redis还是Memcached?*](http://www.jianshu.com/p/7c44168ce922)
1. [redis常用知识点](http://www.jianshu.com/p/8bd815fafbaf)
1. [redis 数据类型详解 以及 redis适用场景场合](http://www.jianshu.com/p/91225223604a)
1. [Redis入门](http://www.jianshu.com/p/86eee4c13645)



## Tutorial

---

1. []()
1. []()
