# 问题

b站会有一些数据会设置 ttl，定期自动删除，怎么去改造rocksdb呢？ b站的分布式kv 是基于 raft + rocksdb，当时答的是需要改造下 rocksdb 的 compaction filter。但会引入一个问题，就是三个 replica 各自去 compaction可能会出现有的 replica 对超过ttl的数据gc了，有的没有，导致不一致，问怎么解决？
对一组事务，每个事务读写 N 个kv对，怎么去做事务并发（不能用锁）？

印象比较深的一个场景题：比如给 kv master 上 raft，然后client 会去向master拿数据，如果采用 round-robin机制，会存在一个问题，就是向副本 A 拿数据后，可能后面会向副本 B 拿数据，但如果 B 的进度比 A 慢，可能会引起 client 的缓存发生回退，怎么解决这种case？

