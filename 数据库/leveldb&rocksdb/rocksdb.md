# 问题

* 讲讲 rocksdb 的 column family
* 你刚刚有提到过 MyRocks，那我看你简历也有写了解RocksDB，要不你给我讲讲lsm-tree？
levelDB跟RocksDB有什么区别？
* 简单介绍下rocksdb
* RocksDB当中DB对象使用单例模式禁止copy
* rocksdb引擎的优点，LSM树讲解
* 定位到rocksdb的迭代器性能问题，去看了下它的迭代器实现，找到问题原因，解决问题。中间还遇到瓶颈在kernel page fault上，也优化了下
* 谈到rocksdb了，开始问rocksdb
* 架构，包括memtable、immem、sst这些烂大街的
* memtable skiplist vs. rb-tree
* 跨cf原子性
* disable掉wal后有没有办法保证跨cf原子性，然后开始引导我，讨论了下可以O_DIRECT + fsync的manifest实现
* rocksdb 压缩


* 详细介绍NoSQL的项目（滴滴做的RocksDB二次开发？）
  * 讲讲LSM-Tree
  * MemTable直接到SST吗？（中间还有个immutable）
  * 你写的SST多大？大小怎么权衡
  * 布隆过滤器介绍一下，如果每个SST有一个BloomFilter，读盘还是有很大的开销，怎么办？（我说的全局BF，但是要考虑数据量大的时候BF的大小还有命中率）
  * 缓存粒度多大？DataBlock，KV还是SST，这几种粒度又各有什么问题，你为什么这么选择？
  * 并发策略的粒度，MVCC是针对KV还是SST
  * MemTable转Immutable没落盘时怎么读的？（糟了没考虑到，可能是整个面试里比较有问题的地方了，现场思考应对了一下）

rocksdb-> 与 mysql 存储引擎对比？
 rocksdb读写