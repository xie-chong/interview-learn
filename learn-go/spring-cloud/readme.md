SpringCloud 相关知识


[01 | 微服务概述与SpringCloud](#01)   

---
---

[Martin Fowler论文](https://martinfowler.com/articles/microservices.html)


就目前而言，**对于微服务业界并没有统一的、标准的定义**（While there is no precise definition of thid architectural）。

但通常而言，微服务架构时一种架构模式或者说是一种架构风格，**它提倡将单一应用程序划分成一组小的服务**，每个服务运行在其独立的自己的**进程中**，服务之间互相协调、互相配合，为用户提供最终价值。服务之间采用轻量级的通信机制互相沟通（通常是基于HTTP的RESTful API）。每个服务都围绕着具体业务进行构建，并且能够被独立的部署到生产环境、类生产环境等。另外，应尽量避免统一的、集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建，可以有一个非常轻量级的集中式管理来协调这些服务，可以使用不同的语言来编写程序，也可以使用不同的数据存储。




---
<h2 id="01">01 | 微服务概述与SpringCloud</h2>

---

### 微服务与微服务架构（技术维度理解）:   
微服务化的核心就是将传统的一站式应用，根据业务拆分成一个一个的服务，彻底地去耦合,每一个微服务提供单个业务功能的服务，一个服务做一件事，从技术角度看就是一种小而独立的处理过程，类似进程概念，能够自行单独启动或销毁，拥有自己独立的数据库。


### 微服务技术栈有哪些

微服务条目落地技术备注服务开发Springboot、Spring、SpringMVC服务配置与管理Netflix公司的Archaius、阿里的Diamond等服务注册与发现Eureka、Consul、Zookeeper等服务调用Rest、RPC、gRPC服务熔断器Hystrix、Envoy等负载均衡Ribbon、Nginx等服务接口调用(客户端调用服务的简化工具)Feign等消息队列Kafka、RabbitMQ、ActiveMQ等服务配置中心管理SpringCloudConfig、Chef等服务路由（API网关）Zuul等服务监控Zabbix、Nagios、Metrics、Spectator等全链路追踪Zipkin，Brave、Dapper等服务部署Docker、OpenStack、Kubernetes等数据流操作开发包SpringCloud Stream（封装与Redis,Rabbit、Kafka等发送接收消息）事件消息总线Spring Cloud Bus......
 

### SpringCloud是什么？

#### 1. 官网说明   
* SpringCloud，基于SpringBoot提供了一套微服务解决方案，包括服务注册与发现，配置中心，全链路监控，服务网关，负载均衡，熔断器等组件，除了基于NetFlix的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。
 
* SpringCloud利用SpringBoot的开发便利性巧妙地简化了分布式系统基础设施的开发，SpringCloud为开发人员提供了快速构建分布式系统的一些工具，包括配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等,它们都可以用SpringBoot的开发风格做到一键启动和部署。
 
* SpringCloud并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过SpringBoot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包

SpringCloud = 分布式微服务架构下的一站式解决方案，是各个微服务架构落地技术的集合体，俗称微服务全家桶。

#### 2. SpringCloud和SpringBoot是什么关系？

SpringBoot专注于快速方便的开发单个个体微服务。

SpringCloud是关注全局的微服务协调整理治理框架，它将SpringBoot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供，配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。
 
SpringBoot可以离开SpringCloud独立使用开发项目，但是SpringCloud离不开SpringBoot，属于依赖的关系。
 
SpringBoot专注于快速、方便的开发单个微服务个体，SpringCloud关注全局的服务治理框架。


#### 3. Dubbo是怎么到SpringCloud的？哪些优缺点让你去技术选型？

目前成熟的互联网架构（分布式+服务治理Dubbo）.

**SpringCloud VS DUBBO**:   

最大区别：SpringCloud抛弃了Dubbo的RPC通信，采用的是基于HTTP的REST方式。
严格来说，这两种方式各有优劣。虽然从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原生RPC带来的问题。而且REST相比RPC更为灵活，服务提供方和调用方的依赖只依靠一纸契约，不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适。
 
品牌机与组装机的区别
很明显，Spring Cloud的功能比DUBBO更加强大，涵盖面更广，而且作为Spring的拳头项目，它也能够与Spring Framework、Spring Boot、Spring Data、Spring Batch等其他Spring项目完美融合，这些对于微服务而言是至关重要的。使用Dubbo构建的微服务架构就像组装电脑，各环节我们的选择自由度很高，但是最终结果很有可能因为一条内存质量不行就点不亮了，总是让人不怎么放心，但是如果你是一名高手，那这些都不是问题；而Spring Cloud就像品牌机，在Spring Source的整合下，做了大量的兼容性测试，保证了机器拥有更高的稳定性，但是如果要在使用非原装组件外的东西，就需要对其基础有足够的了解。
 
社区支持与更新力度
最为重要的是，DUBBO停止了5年左右的更新，虽然2017.7重启了。对于技术发展的新需求，需要由开发者自行拓展升级（比如当当网弄出了DubboX），这对于很多想要采用微服务架构的中小软件组织，显然是不太合适的，中小公司没有这么强大的技术能力去修改Dubbo源码+周边的一整套解决方案，并不是每一个公司都有阿里的大牛+真实的线上生产环境测试过。

**总结Cloud与Dubbo**:   

问题：   
曾风靡国内的开源 RPC 服务框架 Dubbo 在重启维护后，令许多用户为之雀跃，但同时，也迎来了一些质疑的声音。互联网技术发展迅速，Dubbo 是否还能跟上时代？Dubbo 与 Spring Cloud 相比又有何优势和差异？是否会有相关举措保证 Dubbo 的后续更新频率？
 
人物：Dubbo重启维护开发的刘军，主要负责人之一
 
刘军，阿里巴巴中间件高级研发工程师，主导了 Dubbo 重启维护以后的几个发版计划，专注于高性能 RPC 框架和微服务相关领域。曾负责网易考拉 RPC 框架的研发及指导在内部使用，参与了服务治理平台、分布式跟踪系统、分布式一致性框架等从无到有的设计与开发过程。



















