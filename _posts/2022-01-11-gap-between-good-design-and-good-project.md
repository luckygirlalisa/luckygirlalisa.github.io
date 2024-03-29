---
layout: post
title: "为什么架构看起来很牛逼的项目做成了没人愿意看的烂项目"
description: ""
category: 
tags: []
---
{% include JB/setup %}
一个乍一看很不错的项目，仔细一看却是很难看懂业务逻辑，开发人员都不愿意认真深究的项目。。

#### 看起来牛逼的架构：
Event driven松耦合
	使用spring event，看起来也中规中矩，发布和监听事件操作正常。
异步完成流程，快速相应
	一个超长流程的预定服务毫秒级别就可返回。
Seata分布式事务管理服务
	采用SAGA事务模式，回滚流程齐全。
dubbo微服务框架提供RPC通信与微服务治理
	产品信息管理服务，订单服务完全分离，各个不同职责的服务通过dubbo service被实现和消费。


#### 然而：
上千行的方法一层套一层，看到十分之一时已经不知道自己在干什么了。

很显然，没有什么实质性的测试，无谈测试覆盖率，以至于想从测试入手尝试理解业务之企图被扼杀在摇篮中。

可以想象，写上千行方法的作者自己也许一周后已经忘记自己在什么地方写过什么方法，可以被复用，以至于同样的代码在不同地方不断重复；或者作者知道自己在某个地方写过，因为嵌套在几千行的方法里，水太深，没法直接调用，还是ctrl+c, ctrl+v需要的时间更短。

各种magic的constant随意躺在代码各处，为后来的读者创造各种猜谜机会。

不知所云的字段名拼成一大段一大段的代码，结合数据库里存储的数据联合想象，啊~~终于理解了作者当初的用意。

本来To C的项目，高性能的架构设计，低耦合的服务结构，应该是相对很modern的项目了，很受程序员小伙伴们喜欢，然而，大部分时间花在猜谜上，就不那么友好了。

#### 为什么呢？
这可能是非技术问题解决技术问题的一个好例子。

接项目时如果能得到更系统化的业务逻辑分享，“猜谜”时是否就没有那么痛苦？

一个可以随时沟通的业务专家，随时能拿到一手确切的需求，是否就可以把更多聪明才智发挥在创造业务价值上？

一个持续更新的模型释义和关系图是否能让团队各个角色的小伙伴眉头舒展多一些？

一个最初设计很不错的架构在落地时能秉承最初理念，用可读可测的代码呈现，这个项目会不会变成一个人见人爱的明星项目？

#### 后话
也许任何一个项目持续时间长了都可能会变形，都需要被治理，但被治理的成本可能比重新实现的成本更高。

愿程序员小伙伴们都能够“人人为我我为人人”，无论是否在一个公司，让码农的世界少些秃顶~
