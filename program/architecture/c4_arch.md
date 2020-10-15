# C4-Model 架构

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [C4-Model 架构](#c4-model-架构)
  - [什么是 C4-Model？](#什么是-c4-model)
    - [人（Person）](#人person)
    - [软件系统（Software System）](#软件系统software-system)
    - [容器（Container）](#容器container)
    - [组件（Component）](#组件component)
  - [C4 图的几个元素](#c4-图的几个元素)
  - [C4 的几张核心图](#c4-的几张核心图)
    - [语境图(System Context Diagram)](#语境图system-context-diagram)
    - [容器图(Container Diagram)](#容器图container-diagram)
    - [组件图(Component Diagram)](#组件图component-diagram)
    - [类图(Code/Class Diagram)](#类图codeclass-diagram)
  - [visual code 如何画 C4](#visual-code-如何画-c4)
  - [参考文档](#参考文档)

<!-- /code_chunk_output -->

## 什么是 C4-Model？

架构图要明确面向人群，根据面向人群的不同，产出四幅图来描述一个系统或者一个架构。

System Context --> Container --> Component --> Code 四个层次。

在 C4 模型中软件系统被抽象为几个部分：

- （人）Person
- （软件系统）Software System
- （容器）Container
- （组件）Component
- （代码）Code

**当我们看待真实世界的“架构图”时候，是要不停的缩放，在每一个层次刻意忽略一些细节才能表达好当前抽象层次的信息。** 类似下面的地图一样，不同层级需要关注的点是不一样的。

![地图缩放](map.webp)
![架构抽象](c4_model.png)

### 人（Person）

这一抽象的提前是软件系统中“人”的部分与”物“分离，例如角色，或者外部子系统等。

### 软件系统（Software System）

软件系统是最高级别的抽象，它描述了一些可以为用户带来价值的东西，无论他们是不是“人”。这包括您正在建模的软件系统，以及该软件系统所依赖的其他软件系统。

### 容器（Container）

容器代表托管代码或数据的事物。为了使整个软件系统正常工作，必须运行一个容器。实际上，容器类似于服务器端的应用程序、一个手机 APP，又或者是一个数据库。

### 组件（Component）

“组件”一词在软件开发行业中是一个非常重载的术语，但是在这种情况下，组件只是封装在定义良好的接口后面的一组相关功能。如果您使用的是 Java 或 C＃之类的语言，则想到组件的最简单方法是它是接口后面的实现类的集合。这些组件的打包方式（例如，每个 JAR 文件，DLL，共享库中的一个组件与许多组件）的打包方式是一个单独且相互关注的问题。

Note：容器内的所有组件通常都在同一处理空间中执行。

## C4 图的几个元素

- 关系——带箭头的线
- 元素——方块和角色
- 关系描述——线上的文字
- 元素的描述——方块和角色里的文字
- 元素的标记——方块和角色的颜色、虚线框

## C4 的几张核心图

### 语境图(System Context Diagram)

![system_context](system_context.png)

作用：

- 构建的系统是什么
- 谁会用它
- 如何融入已有的 IT 环境

### 容器图(Container Diagram)

![container](container.png)

作用：

- 展现了软件系统的整体形态
- 体现了高层次的技术决策
- 系统中的职责是如何分布的，容器间的是如何交互的
- 告诉开发者在哪里写代码

### 组件图(Component Diagram)

![component](component.png)

作用：

- 描述了系统由哪些组件/服务组成
- 厘清了组件之间的关系和依赖
- 为软件开发如何分解交付提供了框架

### 类图(Code/Class Diagram)

![code](code.png)

## visual code 如何画 C4

导入预定义好的一些结构：https://github.com/RicardoNiepel/C4-PlantUML

## 参考文档

- [可视化架构设计——C4 介绍](https://www.jianshu.com/p/33c6a7ed126f)
- [如何画出一张合格的技术架构图？](https://zhuanlan.zhihu.com/p/62172175)
