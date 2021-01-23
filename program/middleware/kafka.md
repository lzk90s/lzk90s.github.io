<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. kafka 数据传输的三种事务定义](#1-kafka-数据传输的三种事务定义)
- [2. kafka 架构](#2-kafka-架构)
- [3. Kafka 消息的有序保证](#3-kafka-消息的有序保证)
- [4. kafka producer 的消息发送到哪一个 partition](#4-kafka-producer-的消息发送到哪一个-partition)
- [5. kafka 为什么那么快](#5-kafka-为什么那么快)
- [6. kafka 中 zookeeper 的作用](#6-kafka-中-zookeeper-的作用)
- [7. kafka producer 发送数据，ack 为 0， 1， -1 的时候代表啥？request.required.acks](#7-kafka-producer-发送数据ack-为-0-1-1-的时候代表啥requestrequiredacks)
- [8. Kafka 判断一个节点是否还活着有那两个条件？](#8-kafka-判断一个节点是否还活着有那两个条件)
- [9. ISR（in-sync replicas）是什么？](#9-isrin-sync-replicas是什么)
- [10. kafka 再平衡](#10-kafka-再平衡)
- [11. kafka 如何保证消息只发送一次](#11-kafka-如何保证消息只发送一次)

<!-- /code_chunk_output -->

## 1. kafka 数据传输的三种事务定义

1. 最多一次 消息不会被重复发送，最多被传输一次，但也有可能一次都不传输
2. 最少一次 消息不会被漏发送，最少被传输一次，但也有可能被重复传输
3. 精确一次 不会漏传输也不会重复传输，仅被传输一次

## 2. kafka 架构

![kafka](kafka.png)

Producer：消息的产生者，Producer 在写入数据的时候永远的找 leader
Broker：Broker 是 kafka 实例
Topic：消息的主题，可以理解为消息的分类，kafka 的数据就保存在 topic。在每个 broker 上都可以创建多个 topic。
Partition：Topic 的分区，每个 topic 可以有多个分区，分区的作用是做负载，提高 kafka 的吞吐量。同一个 topic 在不同的分区的数据是不重复的，partition 的表现形式就是一个一个的文件夹
Replication:每一个分区都有多个副本，副本的作用是做备胎。当主分区（Leader）故障的时候会选择一个备胎（Follower）上位，成为 Leader。
Consumer Group：我们可以将多个消费组组成一个消费者组，一个 partation 只能被消费者组中的一个 consumer 消费，一个 consumer 可以消费多个 partation.
Zookeeper：kafka 集群依赖 zookeeper 来保存集群的的元信息，来保证系统的可用性。

## 3. Kafka 消息的有序保证

1. Kafka 只支持在一个 Partition 中消息时有序的
2. 可以通过 key 来保证同一类型的数据发送至同一 Partition 来保证有序

## 4. kafka producer 的消息发送到哪一个 partition

1. partition 在写入的时候可以指定需要写入的 partition，如果有指定，则写入对应的 partition。
2. 如果没有指定 partition，但是设置了数据的 key，则会根据 key 的值 hash 出一个 partition。
3. 如果既没指定 partition，又没有设置 key，则会轮询选出一个 partition。

## 5. kafka 为什么那么快

- 顺序读写
- 零拷贝
- 分区
- 批量发送
- 主动 poll

## 6. kafka 中 zookeeper 的作用

1. 记录 broker 的状态，broker 会向 zookeeper 发送心跳请求来上报自己的状态。
2. 选举 controller
3. 记录 ISR，ISR（in-sync replica） 是 partition 的一组同步集合，就是所有 follower 里面同步最积极的那部分。一条消息只有被 ISR 中的成员都接收到，才被视为“已同步”状态。只有处于 ISR 集合中的副本才有资格被选举为 leader。
4. node 和 topic 注册，zookeeper 保存了所有 node 和 topic 的注册信息，可以方便的找到每个 broker 持有哪些 topic。
5. topic 配置，zookeeper 保存了 topic 相关配置，例如 topic 列表、每个 topic 的 partition 数量、副本的位置等等。

## 7. kafka producer 发送数据，ack 为 0， 1， -1 的时候代表啥？request.required.acks

- 0：生产者只管发送，不管服务器，消费者是否收到信息
- 1：只有当 leader 确认了收到消息，才确认此消息发送成功
- -1： producer 需要等待 ISR 中的所有 follower 都确认接收到数据后才算一次发送完成，可靠性最高

## 8. Kafka 判断一个节点是否还活着有那两个条件？

1. 节点必须可以维护和 ZooKeeper 的连接，Zookeeper 通过心跳机制检查每个节点的连接
2. 如果节点是个 follower,他必须能及时的同步 leader 的写操作，延时不能太久。

## 9. ISR（in-sync replicas）是什么？

In-Sync Replicas isr 是一个副本的列表，里面存储的都是能跟 leader 数据一致的副本

## 10. kafka 再平衡

## 11. kafka 如何保证消息只发送一次

开启幂等 producer，当 kafka 根据 producerId 和 sequence 判定是同一个消息时，直接丢弃，以此来实现幂等性。
