---
title: "Rabbitmq基础"
date: 2019-12-15T09:50:54+08:00
lastmod: 2019-12-15T09:50:54+08:00
draft: false
tags: ['Rabbitmq']
categories: ['MiddleWare']
---

# Rabbitmq基础

## 基本概念

1. **Exchange**: 交换机，负责讲消息投递到对应的队列。包括四种基本类型。
   - fanout ：广播
   - direct ：把消息路由到那些BindingKey 和RoutingKey完全匹配的队列中。“点对点”
   - topic ：模糊匹配。`*`匹配一个单词，`#`匹配0~任意个单词。
   - headers ：根据发送的消息内容中的headers 属性进行匹配，不常用。
2. **RoutingKey**: 路由键。指定消息的路由规则，一般和**BindingKey**联合使用，确定这个消息的路由规则。
3. **Binding**：Rabbi tMQ 中通过绑定将交换器与队列关联起来。在绑定的时候一般会指定一个绑定键( **BindingKey** ) 。
4. **Connection** ：一条和RabbitMQ Broker 的TCP连接。
5. **Channel**：信道是建立在Connection 之上的虚拟连接，每个信道都会被指派一个唯一的ID。多个信道可以复用一条TCP连接。（类似NIO的思想）

---

ps：在不同的交换机类型下，BingdingKey和RoutingKey可能是同一种东西（尤其是在使用direct 类型的交换器的时候）。具体情况需要具体分析。



