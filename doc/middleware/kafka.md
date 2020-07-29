# kafka

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [kafka](#kafka)
  - [kafka 数据传输的三种事务定义](#kafka-数据传输的三种事务定义)
  - [kafka 架构](#kafka-架构)
  - [Kafka 消息的有序保证](#kafka-消息的有序保证)
  - [kafka producer 的消息发送到哪一个 partition](#kafka-producer-的消息发送到哪一个-partition)
  - [kafka 为什么那么快](#kafka-为什么那么快)
  - [kafka 中 zookeeper 的作用](#kafka-中-zookeeper-的作用)

<!-- /code_chunk_output -->

## kafka 数据传输的三种事务定义

1. 最多一次 消息不会被重复发送，最多被传输一次，但也有可能一次都不传输
2. 最少一次 消息不会被漏发送，最少被传输一次，但也有可能被重复传输
3. 精确一次 不会漏传输也不会重复传输，仅被传输一次

## kafka 架构

![kafka](kafka.png)

Producer：消息的产生者，Producer 在写入数据的时候永远的找 leader
Broker：Broker 是 kafka 实例
Topic：消息的主题，可以理解为消息的分类，kafka 的数据就保存在 topic。在每个 broker 上都可以创建多个 topic。
Partition：Topic 的分区，每个 topic 可以有多个分区，分区的作用是做负载，提高 kafka 的吞吐量。同一个 topic 在不同的分区的数据是不重复的，partition 的表现形式就是一个一个的文件夹
Replication:每一个分区都有多个副本，副本的作用是做备胎。当主分区（Leader）故障的时候会选择一个备胎（Follower）上位，成为 Leader。
Consumer Group：我们可以将多个消费组组成一个消费者组，一个 partation 只能被消费者组中的一个 consumer 消费，一个 consumer 可以消费多个 partation.
Zookeeper：kafka 集群依赖 zookeeper 来保存集群的的元信息，来保证系统的可用性。

## Kafka 消息的有序保证

1. Kafka 只支持在一个 Partition 中消息时有序的
2. 可以通过 key 来保证同一类型的数据发送至同一 Partition 来保证有序

## kafka producer 的消息发送到哪一个 partition

1. partition 在写入的时候可以指定需要写入的 partition，如果有指定，则写入对应的 partition。
2. 如果没有指定 partition，但是设置了数据的 key，则会根据 key 的值 hash 出一个 partition。
3. 如果既没指定 partition，又没有设置 key，则会轮询选出一个 partition。

## kafka 为什么那么快

- Cache Filesystem Cache PageCache 缓存
- 顺序写 由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机写内存还要快。
- Zero-copy 零拷技术减少拷贝次数
- Batching of Messages 批量量处理。合并小的请求，然后以流的方式进行交互，直顶网络上限。
- Pull 拉模式 使用拉模式进行消息的获取消费，与消费端处理能力相符。

## kafka 中 zookeeper 的作用

1. 记录 broker 的状态，broker 会向 zookeeper 发送心跳请求来上报自己的状态。
2. 选举 controller
3. 记录 ISR，ISR（in-sync replica） 是 partition 的一组同步集合，就是所有 follower 里面同步最积极的那部分。一条消息只有被 ISR 中的成员都接收到，才被视为“已同步”状态。只有处于 ISR 集合中的副本才有资格被选举为 leader。
4. node 和 topic 注册，zookeeper 保存了所有 node 和 topic 的注册信息，可以方便的找到每个 broker 持有哪些 topic。
5. topic 配置，zookeeper 保存了 topic 相关配置，例如 topic 列表、每个 topic 的 partition 数量、副本的位置等等。
