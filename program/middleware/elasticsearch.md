# elasticsearch

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [elasticsearch](#elasticsearch)
  - [倒排索引原理](#倒排索引原理)
  - [Lucene FST 是什么？](#lucene-fst-是什么)
  - [分词](#分词)
  - [ES DSL](#es-dsl)
    - [Query Context](#query-context)
    - [Filter Context](#filter-context)
    - [bool 复合查询](#bool-复合查询)
  - [ES 数据写入过程](#es-数据写入过程)
    - [ES 段合并](#es-段合并)
  - [ES 删除数据过程](#es-删除数据过程)
  - [ES 搜索过程](#es-搜索过程)
    - [ES 如何解决深度分页问题](#es-如何解决深度分页问题)
  - [ES 是如何实现 master 选举的？](#es-是如何实现-master-选举的)
  - [如何解决 ES 集群的脑裂问题](#如何解决-es-集群的脑裂问题)
  - [ES 调优的手段？](#es-调优的手段)
  - [参考](#参考)

<!-- /code_chunk_output -->

## 倒排索引原理

传统的检索方式是通过文章，逐个遍历找到对应关键词的位置。
倒排索引，是通过分词策略，形成了词和文章的映射关系表，也称倒排表，这种词典 + 映射表即为倒排索引。

其中词典中存储词元，倒排表中存储该词元在哪些文中出现的位置。
有了倒排索引，就能实现 O(1) 时间复杂度的效率检索文章了，极大的提高了检索效率。

倒排索引的底层实现是基于：FST（Finite State Transducer）数据结构。
1）空间占用小。通过对词典中单词前缀和后缀的重复利用，压缩了存储空间；
2）查询速度快。O(len(str))的查询时间复杂度。

## Lucene FST 是什么？

## 分词

## ES DSL

### Query Context

Query Context 中的语句要计算 score

### Filter Context

Filter Context 中的语句不计算 score

### bool 复合查询

- must
- must_not
- should
- filter

## ES 数据写入过程

1. 客户端向集群某节点写入数据，发送请求。（如果没有指定路由/协调节点，请求的节点扮演协调节点的角色。）

2. 协调节点接受到请求后，默认使用文档 ID 参与计算（也支持通过 routing），得到该文档属于哪个分片。随后请求会被转到另外的节点。

```
路由算法：根据文档id或路由计算目标的分片id
shard = hash(document_id) % (num_of_primary_shards)
```

3. 当分片所在的节点接收到来自协调节点的请求后，会将请求写入到 Memory Buffer，然后定时（默认是每隔 1 秒）写入到 F ilesystem Cache，这个从 Momery Buffer 到 Filesystem Cache 的过程就叫做 refresh；
4. 当然在某些情况下，存在 Memery Buffer 和 Filesystem Cache 的数据可能会丢失，ES 是通过 translog 的机制来保证数据的可靠性的。其实现机制是接收到请求后，同时也会写入到 translog 中，当 Filesystem cache 中的数据写入到磁盘中时，才会清除掉，这个过程叫做 flush；
5. 在 flush 过程中，内存中的缓冲将被清除，内容被写入一个新段，段的 fsync 将创建一个新的提交点，并将内容刷新到磁盘，旧的 translog 将被删除并开始一个新的 translog。
6. flush 触发的时机是定时触发（默认 30 分钟）或者 translog 变得太大（默认为 512 M）时。

### ES 段合并

- Lucene 索引是由多个段组成，段本身是一个功能齐全的倒排索引。
- 段是不可变的，允许 Lucene 将新的文档增量地添加到索引中，而不用从头重建索引。
- 对于每一个搜索请求而言，索引中的所有段都会被搜索，并且每个段会消耗 CPU 的时钟周、文件句柄和内存。这意味着段的数量越多，搜索性能会越低。
- 为了解决这个问题，Elasticsearch 会合并小段到一个较大的段，提交新的合并段到磁盘，并删除那些旧的小段。（段合并）

## ES 删除数据过程

删除和更新也都是写操作，但是 Elasticsearch 中的文档是不可变的，因此不能被删除或者改动以展示其变更。

磁盘上的每个段都有一个相应的 .del 文件。当删除请求发送后，文档并没有真的被删除，而是在 .del 文件中被标记为删除。该文档依然能匹配查询，但是会在结果中被过滤掉。当段合并时，在 .del 文件中被标记为删除的文档将不会被写入新段。

在新的文档被创建时，Elasticsearch 会为该文档指定一个版本号，当执行更新时，旧版本的文档在 .del 文件中被标记为删除，新版本的文档被索引到一个新段。旧版本的文档依然能匹配查询，但是会在结果中被过滤掉。

## ES 搜索过程

搜索被执行成一个两阶段过程，即 Query Then Fetch；

**一、Query 阶段：**
查询会广播到索引中每一个分片拷贝（主分片或者副本分片）。每个分片在本地执行搜索并构建一个匹配文档的大小为 from + size 的优先队列。

**二、Fetch 阶段:**
协调节点辨别出哪些文档需要被取回并向相关的分片提交多个 GET 请求。每个分片加载并 丰富 文档，如果有需要的话，接着返回文档给协调节点。一旦所有的文档都被取回了，协调节点返回结果给客户端。

### ES 如何解决深度分页问题

可以采用 Scroll 滚动 API，解决深度分页问题

## ES 是如何实现 master 选举的？

7.0 以前是使用的类 Bully 算法。
7.0 以后使用的是类 Raft 算法。

Elasticsearch 7.0 版本中，Elasticsearch 采用了一个新的集群协调模块（Coordinator）代替了旧版 Zen Discovery，对选主算法 Bully 也进行了更换。

## 如何解决 ES 集群的脑裂问题

所谓集群脑裂，是指 Elasticsearch 集群中的节点（比如共 20 个），其中的 10 个选了一个 master，另外 10 个选了另一个 master 的情况。

当集群 master 候选数量不小于 3 个时，可以通过设置最少投票通过数量（discovery.zen.minimum_master_nodes）超过所有候选节点一半以上来解决脑裂问题；
当候选数量为两个时，只能修改为唯一的一个 master 候选，其他作为 data 节点，避免脑裂问题

## ES 调优的手段？

部署层面：

1. 机器内存尽量大一些，filesystem cache 越大越好为了使得搜索速度更快。
2. 尽量使用 SSD，因为查询和索引性能将会得到显著提升。
3. 避免集群跨越大的地理距离，一般一个集群的所有节点位于一个数据中心中。
4. 设置堆内存：节点内存/2，不要超过 32GB。
5. 关闭缓存 swap。内存交换到磁盘对服务器性能来说是致命的。
6. 增加文件描述符，设置一个很大的值，如 65535。Lucene 使用了大量的文件，同时，Elasticsearch 在节点和 HTTP 客户端之间进行通信也使用了大量的套接字。所有这一切都需要足够的文件描述符。

索引层面：

1. 使用批量请求并调整其大小：每次批量数据 5–15 MB 大是个不错的起始点。
2. 如果你的搜索结果不需要近实时的准确度，考虑把每个索引的 index.refresh_interval 改到 30s。
3. 如果你在做大批量导入，考虑通过设置 index.number_of_replicas: 0 关闭副本。
4. 需要大量拉取数据的场景，可以采用 scan & scroll api 来实现，而不是 from/size 一个大范围。

存储层面：

1. 基于数据+时间滚动创建索引，每天递增数据。控制单个索引的量，一旦单个索引很大，存储等各种风险也随之而来，所以要提前考虑+及早避免。
2. 冷热数据分离存储，热数据（比如最近 3 天或者一周的数据），其余为冷数据。对于冷数据不会再写入新数据，可以考虑定期 force_merge 加 shrink 压缩操作，节省存储空间和检索效率。

## 参考

https://www.wenyuanblog.com/blogs/elasticsearch-interview-questions.html