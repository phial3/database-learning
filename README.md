

# 数据库/存储学习路径推荐

我自己就是从业务自学转入数据库内核研发岗位的，根据自己的经历，简单总结了一下入门数据库/存储相关的学习路线、学习资料、项目书籍推荐等，大家可以参考。

并且需要强调的是，这些内容其实并不只是为了想要转入数据库/存储方向的同学。

就算是业务后端开发，以及云原生等方向，数据库、分布式都是必备的基础内容，并且存储也能够学习到很多的一些操作系统基础知识，所以我觉得都是有必要去进行学习的。

## 必看课程
CMU-15445 和 CMU-15721
[https://www.youtube.com/@CMUDatabaseGroup](https://www.youtube.com/@CMUDatabaseGroup)

这两个不用多说，经典的数据库入门教程，由数据库的大佬 Andy Pavlo 亲自授课。

可以了解到数据库的基本概念，例如存储、BufferPool 缓冲池、索引、优化器、执行器、事务等。

15445 的实验部分是基于其开源的教学项目 [bustub](https://github.com/cmu-db/bustub)，补全其中几个重要的部分，这个项目是 C++ 写的，如果对 C++ 不熟悉的话，那么我觉得实验部分可以暂时跳过，有多余的精力再来搞，毕竟我们是来学数据库的，而不是学 C++ 的。

## 存储项目实践
学习数据库课程的同时，顺便可以了解下存储方面的内容，存储是数据库必不可缺的重要组成部分。

例如 B+ 树、bitcask、LSM Tree，以及 LSM Tree 的优化 Wisckey，找几篇文章看看，了解下基本概念，或者直接看看对应的最权威的论文。

然后自己去实践写一个，例如写一个简单的 bitcask、B+ 树存储引擎，或者 LSM 存储引擎。

在我的经验看来，这几类存储引擎，实现的难度大致是 B+树 >= LSM > bitcask，bitcask 可以看做是一个简化版的 LSM Tree。

对于 B+ 树，学习的资料有

- [BoltDB](https://github.com/etcd-io/bbolt)，Go 语言写的一个 B+ 树单机存储引擎，在生产环境中广泛应用
- [https://cstack.github.io/db_tutorial](https://cstack.github.io/db_tutorial/)，一个简单的从零开始写数据库的教程，类似 SQLite，C 语言实现

LSM Tree，可以参考以下资料

- [leveldb](https://github.com/google/leveldb)，Google 开源的单机存储引擎，Go 语言也有一个对应的实现 [goleveldb](https://github.com/syndtr/goleveldb)
- [leveldb-rs](https://github.com/dermesser/leveldb-rs)，Rust 实现的 leveldb
- [mini-lsm](https://github.com/skyzh/mini-lsm)，一个从零实现极简的 LSM 教程，Rust 实现

bitcask 可参考资料：

- [rosedb](https://github.com/rosedblabs/rosedb)，基于 bitcask 的单机 KV 引擎
- [mini-bitcask](https://github.com/rosedblabs/mini-bitcask)，一个极简的 bitcask 教程

之所以推荐写存储类的实战项目，主要是因为存储层的 KV 一般比较好实现，同时又能够了解到一些数据库的基本设计理念。

**强烈推荐👍🏻**
如果想要一个完整实现 KV 存储的代码实践教程，可以参考我的[《从零实现 KV 存储》](https://w02agegxg3.feishu.cn/docx/Ktp3dBGl9oHdbOxbjUWcGdSnn3g)的教程，从第一行代码开始的视频实战教程，使用 Go 和 Rust 两种语言分别实现。

## 存储引擎

存储引擎底层实现的方式主要包括但不限于以下几种：

#### 1. B+Tree：

B+Tree 是一种平衡树数据结构，广泛应用于数据库和文件系统的索引结构中。在数据库存储引擎中，如 MySQL 的 InnoDB 存储引擎，默认使用的就是基于 B+Tree 的索引结构。B+Tree 所有的叶子节点形成了一个有序链表，适合于区间查询和全表扫描，且由于所有数据都在叶子节点，每次查询只需要访问叶子节点，从而减少了磁盘I/O次数。

#### 2. Hash：

哈希存储引擎通常用于内存数据库或缓存系统，如 Redis 和 Memcached。哈希表允许通过哈希函数对键进行直接寻址，理论上提供 O(1) 的时间复杂度，适合于快速查找、插入和删除操作，但不支持范围查询和排序。

#### 3. LSM-Tree（Log-Structured Merge Tree）：

LSM-Tree 结构的存储引擎，如 LevelDB、RocksDB、Cassandra 等，适用于写入密集型场景。数据首先被写入内存中的数据结构（称为 memtable），当内存中的数据达到一定阈值时，会被刷入磁盘，并通过一系列合并操作来优化读取性能和磁盘空间利用率。

#### 4. Bitmap / Roaring Bitmaps：

位图索引在某些特定场景下非常高效，尤其是当索引的基数较低时，如统计某属性的各种状态时。Roaring Bitmaps 是位图的一种优化实现，能在内存中高效表示稀疏整数集合。

#### 5. WAL（Write-Ahead Log）/Append-Only File：

许多数据库存储引擎采用预写日志（WAL）机制来保证数据的持久化和一致性，例如 PostgreSQL 和 SQLite。数据先写入日志文件，然后再写入数据文件，以此降低数据丢失的风险，并支持崩溃恢复。

#### 6. Log-Structured File System (LFS)：

类似于 LSM-Tree，日志结构文件系统将写入操作作为日志记录，然后通过后台合并来优化读取性能，例如 Facebook 的 RocksDB 就采用 LFS 的思想。

#### 7. Column-Oriented Storage：

列式存储引擎将数据按列存储而非按行存储，适合于大数据分析场景，因为同列数据可以连续存储，从而大大提高了大数据量的批量查询效率，如 Apache Parquet、Cassandra 等。

#### 8. 图存储引擎：

图存储引擎用于存储和查询图结构数据，如 Neo4j 使用了特有的 Property Graph Model，并实现了一套专为图数据设计的索引结构。

## 事务/MVCC
这部分网上的资料比较多，可以看看事务的一些基本概念 ACID，然后看看如何去实现的，可以借鉴其他数据库例如 MySQL、PostgreSQL，关于事务实现原理分析这方面的文章比较多。

概念了解差不多之后，可以自己动手实现，例如可以在自己写的存储项目的基础上，加上事务的功能，保证事务原子性、隔离性，以及并发读写的性能，自己上手撸肯定比只了解理论好很多。

CMU 15445 和 15721 课程都讲到了事务的一些基本概念，可以参考 [https://15445.courses.cs.cmu.edu/fall2019/schedule.html#oct-23-2019](https://15445.courses.cs.cmu.edu/fall2019/schedule.html#oct-23-2019)

其他的一些部分，例如 parser、执行器、优化器、向量化等等，比较复杂，自己从头搞一个的难度比较大，我觉得可以简单看看资料，了解一下基本概念，工作之中再针对性的查漏补缺。



当然如果你对某个部分特别感兴趣的话，比如优化器之类的，也可以多去了解然后自己实践，我这里推荐存储和事务的实现，是因为相对来说比较容易上手。



其他方面的项目实践这里也推荐一些资料：

- Go 语言实现的 sql 解析器 [https://github.com/xwb1989/sqlparser](https://github.com/xwb1989/sqlparser)
- TiDB 的 mysql 解析器 [https://github.com/pingcap/tidb/tree/master/pkg/parser](https://github.com/pingcap/tidb/tree/master/pkg/parser)
- Rust 实现的 sql parser [https://github.com/sqlparser-rs/sqlparser-rs](https://github.com/sqlparser-rs/sqlparser-rs)
- Meta 开源维护的通用数据库执行框架 Velox [https://github.com/facebookincubator/velox](https://github.com/facebookincubator/velox)
- 向量化执行框架 Arrow [https://github.com/apache/arrow](https://github.com/apache/arrow)
## 分布式
这部分内容首推 MIT 6824（现已改名为 6.5840），分布式系统入门的首选课程。
[https://www.youtube.com/@6.824/videos](https://www.youtube.com/@6.824/videos)

有精力的话可以跟着把实验部分做完。lab 是实现一个除成员变更之外的 raft 共识算法，并且基于 raft 实现一个容错的分布式 KV 系统。

**强烈推荐👍🏻**
如果对课程内容并不熟悉的话，初次上手做 lab 可能会比较痛苦，并且自己搞也可能会比较浪费时间。

可以看看我的[《从零实现分布式 KV》](https://av6huf2e1k.feishu.cn/docx/JCssdlgF4oRADcxxLqncPpRCn5b)教程，从零开始，基于 6824 的 lab，写一个基于 raft 的分布式 KV，教程内容完全涵盖了 6824 的 lab 部分。



这个学完了之后，可以挑战下 PingCAP 的 talent plan 中的 TinyKV，它和 6824 的实验部分比较类似，实现一个基于 raft 的分布式 KV 存储系统，难度会比 6824 更大，lab 的代码框架是现成的，只需要往里面添加内容即可，测试也比较完备。
[https://github.com/talent-plan/tinykv](https://github.com/talent-plan/tinykv)



如果还有时间的话，可以再上一个台阶，挑战下 PingCAP talent plan 的 TinySQL 项目，主要是实现一个简单的分布式数据库项目，有完备的文字教程，只是难度略大。
[https://github.com/talent-plan/tinysql](https://github.com/talent-plan/tinysql)

## 工作或者实习
当然，其实最好的办法，还是能够直接参与到工作实践当中，这样学习起来是最快的，可以向 leader 请教，和同事交流等。

如果自身又没有太多经验的话，可以试试那些愿意接纳转数据库内核的公司，这可能会要求你有其他亮眼的东西了，比如基础比较扎实，折腾过自己的项目之类的。能把上面提到的这些东西认真学习下，完成个 60% 左右，我觉得应对一些面试就应该没有太大的问题了。

---

为了帮助你更高效的学习，我还整理了一份数据库开发的学习资料，数据库的各个方面都涉及到了，例如 SQL、优化器、执行引擎、存储等等，包含一些优质的书籍、论文、视频课程、博客等，还有一些优质的教学类项目。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/12925940/1673770833130-d80eaccc-6fce-4e23-bdc5-5b00d1bb384f.png#averageHue=%23ececec&clientId=ufa50e856-2120-4&from=paste&height=746&id=ua30dabf1&originHeight=1492&originWidth=1700&originalType=binary&ratio=1&rotation=0&showTitle=false&size=296215&status=done&style=none&taskId=u7b00b1aa-bd87-48b7-934f-e9ca536db81&title=&width=850)
总计十几页的 PDF，一次性送给你，方便提升学习效率。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/12925940/1673770714528-fc5cd43d-54c6-4c7f-8c8a-b0e8106fdf3e.png#averageHue=%23f7f7f7&clientId=ufa50e856-2120-4&from=paste&height=615&id=uf81c3cca&originHeight=1230&originWidth=1294&originalType=binary&ratio=1&rotation=0&showTitle=false&size=321082&status=done&style=none&taskId=uca8d9770-71df-4791-9af6-a3cdad4d763&title=&width=647)
还有一些关于数据库方面的优质 PDF 书籍，可以参考学习：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/12925940/1675175402743-f5987338-d67e-4c0d-a574-9c1a7f743ca0.png#averageHue=%23dbd8cf&clientId=u6861054c-f8dc-4&from=paste&height=451&id=u4b53b520&originHeight=902&originWidth=1682&originalType=binary&ratio=1&rotation=0&showTitle=false&size=897755&status=done&style=none&taskId=u6b44cf46-3c87-4c40-be5f-d7d56162dc1&title=&width=841)
**这份学习资料 PDF 和所有的书籍都可以在我的公众号领取**，后台回复关键字**【数据库】**。
![Snipaste_2023-02-01_09-58-05.png](https://cdn.nlark.com/yuque/0/2023/png/12925940/1675216713682-a3cab6f7-93ca-4699-999d-223ba77cbc97.png#averageHue=%23e9e6dc&clientId=u90c0e997-1881-4&from=ui&id=u6f9b9cd5&originHeight=762&originWidth=2800&originalType=binary&ratio=1&rotation=0&showTitle=false&size=260276&status=done&style=none&taskId=u07df7cea-d948-4f48-9257-7cc34779e12&title=)
