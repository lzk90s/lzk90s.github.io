# 常用容器的底层实现

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [常用容器的底层实现](#常用容器的底层实现)
  - [JAVA 中容器的数据结构实现](#java-中容器的数据结构实现)
  - [c++中的容器的数据结构实现](#c中的容器的数据结构实现)
  - [golang 中容器实现](#golang-中容器实现)

<!-- /code_chunk_output -->

需要掌握容器的数据结构，初始化、增、删、改、查、扩容等操作的底层实现

## JAVA 中容器的数据结构实现

![java_container](java_container.png)

| 容器              | 数据结构                                                                                 |
| :---------------- | :--------------------------------------------------------------------------------------- |
| ArrayList         | 数组                                                                                     |
| Vector            | 数组                                                                                     |
| LinkedList        | 双向链表                                                                                 |
| Stack             | Vector                                                                                   |
| HashMap (java1.7) | 底层实现是数组+链表                                                                      |
| HashMap (java1.8) | 数组+链表+红黑树，当冲突链表长度大于 8 时，链表转为红黑树，小于等于 6 时，红黑树转为链表 |
| LinkedHashMap     | HashMap + 双向链表，插入顺序有序                                                         |
| TreeMap           | 按照 key 升序                                                                            |
| HashSet           | HashMap                                                                                  |
| LinkedHashSet     | LinkedHashMap + 双向链表（保证插入和读取顺序一致）                                       |
| TreeSet           | 带全排序的集合容器                                                                       |

> 问：java HashMap 和 LinkedHashMap 的区别？
>
> HashMap 是无序的，当我们希望有顺序地去存储 key-value 时，就需要使用 LinkedHashMap 了，LinkedHashMap 默认的构造参数是默认 插入顺序的，就是说你插入的是什么顺序，读出来的就是什么顺序。

## c++中的容器的数据结构实现

| 容器                         | 数据结构 |
| :--------------------------- | :------- |
| map, set                     | 红黑树   |
| unordered_map, unordered_set | 散列表   |
| vector                       | 数组     |
| list                         | 链表     |

## golang 中容器实现

| 容器  | 数据结构 |
| :---- | :------- |
| slice |          |
| map   |          |
