<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. 整体架构](#1-整体架构)
- [2. 启动过程](#2-启动过程)
- [3. 消费者](#3-消费者)
- [4. 生产者](#4-生产者)
  - [4.1. 服务是如何发布的](#41-服务是如何发布的)
- [5. 重要的 SPI 扩展点](#5-重要的-spi-扩展点)
  - [5.1. Invoker](#51-invoker)
  - [5.2. ProxyFactory](#52-proxyfactory)
  - [5.3. Cluster](#53-cluster)
    - [5.3.1. Directory(StaticDirectory, RegistryDirectory)](#531-directorystaticdirectory-registrydirectory)
    - [5.3.2. Router](#532-router)
    - [5.3.3. LoadBalance](#533-loadbalance)
  - [5.4. Protocol](#54-protocol)

<!-- /code_chunk_output -->

## 1. 整体架构

![dubbo](dubbo1.jpg)

![dubbo](dubbo2.jpg)

## 原理

### 服务发现
