# 问题

* leveldb 概述
* leveldb 存储机制
* leveldb 日志
* leveldb 查找机制
* leveldb 如何修改
* 跳表
* 查询有缓存吗
* write_batch 细节
* leveldb 事务
* 讲讲布隆过滤器
* leveldb 怎么优化查询的
* leveldb可以用在多台主机之间吗，我说leveldb是单机的，他问怎么做可以实现分布式，我说用一致性哈希算法，之后他又问一台机器宕机后，需不需要转移数据
* leveldb 和 redis 对比、区别
* leveldb 中数据结构的实现
* leveldb 多路归并
* leveldb 读写放大 
* leveldb 可以加索引吗
* lsm 树介绍
* boltdb、badgerdb 和 leveldb 区别
* 了解 rocksdb 吗 
* leveldb源码怎么看的，看了哪些部分
* memtable的实现、memtable如何实现无锁下的并发读写安全、为什么memtable选择跳表，innodb选择B+树？
* 如果你来优化leveldb，你会怎么优化
* 你刚才说到不同的存储介质，你知道ssd,hhd每秒的IO量级吗
* 说一下你所知道的leveldb、rocksdb的区别
* 如果你来实现多线程compaction,你会怎么实现
* leveldb的出现是为了解决什么问题
* leveldb有什么优点
* leveldb的快照了解吗
* 说一下leveldb读写的过程
* 现在有个黄页数据，比较一下LSM-Tree、哈希存储、B+-Tree三种存储引擎的优劣以及性能瓶颈
* 现在有种新的存储介质，随机读写和顺序读写的速度相近，你有什么优化leveldb的存储引擎的方案
* leveldb 迭代器 ？ 收获？ arena 作用 ？
* immutable memtable dump 成 sstable 时，读的是哪一个？
* 为什么 lsm 对磁盘友好？
* 讲一讲 leveldb 压缩
* 一直添加不相同的key，compaction 还有用吗？
* kv 存储优势，劣势
* 对比 b+ 树，lsm树
* leveldb 读写放大
* 两种写入模式分析 ：in place 模式和 append模式
* 介绍kv存储怎么做优化，讲了下kv分离的实现
* 为什么 LSM 写入速度快
* 是否了解过其他基于leveldb的生产级的数据库
* leveldb的特点
* leveldb使用场景
* 为什么不直接使用redis，而需要有本项目/RocksDB
* 是否考虑怎么在KV数据库项目中加入事务
* leveldb为啥要搞这样的分层设计？
* leveldb为什么要从上层往下层读呢？不能从下层开始？
* leveldb 读哪一层sst最耗时，为什么？
* 如果前台不停的读，后台在做compaction ，会发生什么？
* levelDB 读写？
* levelDB 怎么做 GC ？
* levelDB 这种读写放大怎么解决的？
* leveldb内存有哪几种形式，
* leveldb读数据顺序; leveldb删除数据;
* leveldb和mysql优缺点;
* leveldb如何实现MVCC机制的;
* LSMtree知道，TSM知道吗;
* 导致 write stall 的原因
* leveldb 写入是顺序写入吗
* 多线程并发的时候，leveldb 能保证顺序写吗？
* 为什么 bwtree 相比 lsmtree 的 list 性能好
* levelDB为什么选用skiplist做索引
* levelDB适用于写多读少的问题，讲了随机读取与顺序读取的区别
* 为什么mysql不像levelDB这样做把连续数据存在连续页，并且不使用像levelDB这样的skiplist做索引，或者说基于此做一些拓展呢？我说我想想，（其实我想回答的设计里面的不同,可能mysql为了支持复杂的serach insert操作所以如此）面官微笑着说不用了，下一个吧。。。果然预期所想会问到你答不上来为止。
* leveldb 的优势，劣势
* 读第 0 层 sstable是怎么做的，第 0 层 compaction 怎么做的
* 多线程写，写 wal 会不会压力很大，怎么解决
* leveldb 的写队列需要一把大锁去保护，如果用无锁队列怎么设计
* leveldb 的version是什么，什么时候会变化
* 项目中提到levelDB，让我讲讲levelDB？讲讲跳表？为什么不考虑用其它的数据库？怎么进行的优化？提到Grafana，知不知道Linux上的其它监控软件？为什么不考虑rocksDB
* levelDB的SST文件是利于读还是利于写？
* leveldb 中如果经过多次 compact，底层文件系统产生了很多碎片，WAL 还能保持高效的顺序写性能吗？
* 内存不够用咋办，leveldb 是内存数据库还是磁盘数据库？

* LSM-tree 原理和特点
* 为什么要追加写
* LSM-tree 的工业实现
* LSM-tree 的合并方式（合并超出阈值的部分还是全部？优缺点
* Bloom filter 的实现
* Bloom filter 如何持久化
* 缓存的索引怎么做缓存淘汰
* 缓存的索引一个 SSTable 对应一个 map 用一个全局 map 的优缺点
* 如何做 LSM-tree 的 value 缓存（page cache 有用吗
* LSM-tree 合并的时候怎么处理读请求
* 如何实现 LSM-tree 的 MVCC

导致 write stall 的原因
leveldb 写入是顺序写入吗
多线程并发的时候，leveldb 能保证顺序写吗？

