# 设计模式

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [设计模式](#设计模式)
  - [SOLID 原则](#solid-原则)
    - [单一职责原则(The Single Responsibility Principle )](#单一职责原则the-single-responsibility-principle)
    - [开放封闭原则(The Open Closed Principle )](#开放封闭原则the-open-closed-principle)
    - [里氏替换原则(The Liskov Substitution Principle)](#里氏替换原则the-liskov-substitution-principle)
    - [接口分离原则(The Interface Segregation Principle)](#接口分离原则the-interface-segregation-principle)
    - [依赖倒置原则(The Dependency Inversion Principle )](#依赖倒置原则the-dependency-inversion-principle)
  - [常用设计模式](#常用设计模式)
    - [创建型模式（关注单一对象的生成）](#创建型模式关注单一对象的生成)
      - [简单工厂](#简单工厂)
      - [复杂工厂](#复杂工厂)
      - [原型模式](#原型模式)
      - [单例模式](#单例模式)
        - [单例模式的几种实现方式](#单例模式的几种实现方式)
      - [Builder 模式](#builder-模式)
    - [结构型模式（关注多个对象如何有效组合成新的对象）](#结构型模式关注多个对象如何有效组合成新的对象)
      - [适配器模式](#适配器模式)
      - [桥接模式](#桥接模式)
      - [组合模式](#组合模式)
      - [装饰模式](#装饰模式)
      - [外观模式](#外观模式)
      - [享元模式](#享元模式)
      - [代理模式](#代理模式)
    - [行为型模式（关注对象之间的交互及职责划分）](#行为型模式关注对象之间的交互及职责划分)
      - [职责链模式](#职责链模式)
      - [命令模式](#命令模式)
      - [迭代器模式](#迭代器模式)
      - [中介者模式](#中介者模式)
      - [观察者模式](#观察者模式)
      - [状态模式](#状态模式)
      - [策略模式](#策略模式)
      - [访问者模式](#访问者模式)

<!-- /code_chunk_output -->

## SOLID 原则

### 单一职责原则(The Single Responsibility Principle )

一个类只完成它应该完成的职责

### 开放封闭原则(The Open Closed Principle )

软件实体(类,模块,函数等等)应当对扩展开放，对修改闭合

### 里氏替换原则(The Liskov Substitution Principle)

优先使用组合（委托）而不是继承，或者说“只有在确定是 is-a 的关系时才能使用继承”，因为继承经常导致”紧耦合“的设计

### 接口分离原则(The Interface Segregation Principle)

接口隔离原则表明客户端不应该被强迫实现一些他们不会使用的接口，应该把胖接口中的方法分组，然后用多个接口替代它，每个接口服务于一个子模块。简单地说，就是使用多个专门的接口比使用单个接口要好很多

### 依赖倒置原则(The Dependency Inversion Principle )

抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对抽象（接口）编程，而不是针对实现细节编程

## 常用设计模式

### 创建型模式（关注单一对象的生成）

#### 简单工厂

#### 复杂工厂

#### 原型模式

#### 单例模式

##### 单例模式的几种实现方式

- 饿汉模式：static 变量(线程安全，调用效率高，但是不能延时加载)
- 懒汉式(线程安全，调用效率不高，但是能延时加载)：
- double check + volatile
- 静态内部类实现模式（线程安全，调用效率高，可以延时加载）
- 枚举类（线程安全，调用效率高，不能延时加载，可以天然的防止反射和反序列化调用）

#### Builder 模式

### 结构型模式（关注多个对象如何有效组合成新的对象）

#### 适配器模式

#### 桥接模式

#### 组合模式

#### 装饰模式

#### 外观模式

#### 享元模式

#### 代理模式

### 行为型模式（关注对象之间的交互及职责划分）

#### 职责链模式

#### 命令模式

#### 迭代器模式

#### 中介者模式

#### 观察者模式

#### 状态模式

#### 策略模式

#### 访问者模式
