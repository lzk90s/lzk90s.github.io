# 1. arthas 定位线上问题

![arthas_command](arthas_command.png)

## 1.1. 常用命令

1. sc/sm : search class, search method
2. tt: time tunnel
3. watch: watch express
4. jad: java decompile
5. thread: thread info

## 1.2. 常见应用场景

### 1.2.1. 线上 debug

### 1.2.2. 手动调用指定方法

### 1.2.3. 获取 spring ioc 容器中的 bean

方法一：通过 tt 获取，具有通用性

1. 拦截 RequestMappingHandlerAdapter 类中的 invokeHandlerMethod 方法。

```shell
tt -t org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter invokeHandlerMethod
```

执行完后，随便调用一个需要 spring mvc 处理的方法，都会被监测到。控制台就会打印出来监测的结果，其中有个 index 字段，标示每次的序号，第一次的序号是 1000.

2. 获取指定序号（1000）的监控过程，执行下面的语句。target 指监控到的对象

```shell
tt -i 1000 -w 'target.getApplicationContext().getBean("userDao").queryById(63)'
```

方法二：通过静态方法获取

1. 获取 classloader hash 值

```shell
sc -d *.SpringContextHolder
```

2. 获取 bean

```shell
ognl -c d400943 '@com.tuya.kunlun.thingspro.biz.util.SpringContextHolder@getBean("storageMServiceClient").getTmpFileDownloadUrl("")'
```

### 1.2.4. 函数的参数/返回值/异常信息

```shell
watch com.arthas.demo.controller.UserController * '{params[0],target,returnObj,throwExp'}
```
