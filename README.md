# 简介

实验室

# 目录

* [简介](#简介)
* [安装 Docker Compose](#安装-docker-compose)
* [项目结构](#文件结构)

* 消息队列
    * Kafka
    * NSQ
    * [RabbitMQ](#rabbitmq)

* 代理服务器
    * [OpenResty](#OpenResty)
    * [NGINX](#NGINX)
    * [NGINX Unit](#NGINX-Unit)
    * [Envoy](#Envoy)

* Service Mesh
    * [Istio](#Istio)

* API网关
    * kong

* 非关系型数据库
    * Redis

* 分布式缓存
    * Memcache

* 数据同步
    * otter

* 日志轮切工具
    * logrotate

* 配置中心
    * apollo

* 容器
    * docker
    * Kubernetes

* 分布式搜索引擎
    * Elasticsearch


# 安装 Docker Compose

```bash
$ curl -sSL https://get.docker.com/ | sh
$ sudo pip install docker-compose
```


# 项目结构

```
./
├── tmp           （存放临时文件）
├── container     （存放容器文件）
├── data          （存放数据文件）
├── bin           （存放可执行文件，脚本或二进制文件等）
├── etc           （存放配置文件）
├── htdocs        （存放web项目）
├── app           （存放进程项目）
└── log           （存放日志文件）

```


# RabbitMQ

## 消息

```
├── 转发模型
│       ├── Direct
│       │   ├── 使用
│       │   ├── 设计
│       │   ├── 适用场景
│       │   ├── 不适用场景
│       │   ├── 生产
│       │   │    ├── 新增队列，没消费端会怎样？
│       │   │    └── 队列容量达到上限后会怎样?
│       │   └── 消费
│       │       ├── 与服务端交互
│       │       │    ├── 短连接
│       │       │    └── 长连接
│       │       ├── 处理消息
│       │       │    ├── 水平扩容消费节点是否会消费到同一条消息
│       │       │    ├── 不回复任何信号会在一直收到该消息
│       │       │    │    ├── 每条消息间隔多久?
│       │       │    │    ├── 发了多少次会结束?
│       │       │    │    └── 什么时候结束?
│       │       │    └── 回复确认信号后不再收到该条消息
│       │       ├── 消费节点状态变更
│       │       │    ├── 新增消费节点
│       │       │    │    ├── 先启动消费后声明队列
│       │       │    │    │    └── 
│       │       │    │    ├── 从哪条消息开始接受?
│       │       │    │    └── 对其他已存在的消费节点的影响是什么?
│       │       │    ├── 销毁消费节点
│       │       │    │    ├── 没有任何消费节点时队列是什么状态?
│       │       │    │    ├── 是否会引起消息堆积?
│       │       │    │    ├── 最后消费到的消息是什么时候的?
│       │       │    │    └── 对其他已存在的消费节点的影响是什么?
│       │       │    ├── 中断消费
│       │       │    │    ├── 是否会引起消息堆积?
│       │       │    │    └── 对其他已存在的消费节点的影响是什么?
│       │       │    └── 恢复消费
│       │       │         ├── 是否可以消费到中断期间生产的消息?
│       │       │         └── 对其他已存在的消费节点的影响是什么?
│       │       └── 临时消费节点
│       ├── Fanout
│       ├── Topic
│       ├── Headers
│       └── Invalid
 
```

* 转发模型 > Direct >  使用

生产端

```bash
./bin/rabbitmq/direct_producer.sh
```

消费端

```bash
./bin/rabbitmq/direct_consumer.sh
./bin/rabbitmq/direct_consumer.sh
```

* 转发模型 > Direct >  设计


* 转发模型 > Direct >  生产 > 新增队列，没消费端会怎样？

messages_ready 会累计，占用内存，发生堆积

* 转发模型 > Direct >  消费 > 消费节点状态变更 > 新增消费节点 > 如果此时没声明过队列会抛异常

```bash

$ docker-compose -f etc/docker/compose/app.yml down && docker-compose -f etc/docker/compose/app.yml up -d
$ ./bin/rabbitmq/direct_consumer.sh

```




## 集群

```
├── 集群如何实现
├── 集群节点管理
│       ├── 新增节点
│       ├── 删除节点
│       └── 节点状态如何探知
│ 
├── 发布消息
│       ├── 数据在多节点的状态
│ 
├── 订阅消息
│       ├── 数据在多节点的状态

```



## 监控

```bash
./bin/rabbitmq/monitor.sh
```


## 单播消息

```bash
./bin/rabbitmq/new_task.sh
```

```bash
./bin/rabbitmq/worker.sh
./bin/rabbitmq/worker.sh
```


## 广播消息

```bash
./bin/rabbitmq/broadcast_new_event.sh
```

```bash
./bin/rabbitmq/event_subscriber1.sh
./bin/rabbitmq/event_subscriber2.sh
```

## 消息堆积

* 发送消息。循环执行

```bash
./bin/rabbitmq/new_task.sh
```

*  消费但不确认消息，循环执行

```bash
./bin/rabbitmq/worker_no_ack.sh
```


*  消费且确认消息，循环执行

```bash
./bin/rabbitmq/worker.sh
```



## 消费重试



## 临时队列

```bash
./bin/rabbitmq/new_tmp.sh
```

```bash
./bin/rabbitmq/tmp_subscriber.sh
```

## 参考资料


* http://www.rabbitmq.com/documentation.html

* https://www.jianshu.com/p/79ca08116d57

[Back to TOC](#目录)

# OpenResty

# NGINX

# NGINX Unit


# Envoy

https://www.envoyproxy.io/

# Istio

https://blog.csdn.net/u012211419/article/details/78963276

http://istio.doczh.cn/docs/concepts/what-is-istio/overview.html
