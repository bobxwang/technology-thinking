> 微服务,更多的是一些思维,由一些独立部署的服务来共同组成一个系统,同时单独部署跑在自己独立的进程中,按照业务进行划分,容错,自动化运维,服务间进行轻通信Restful(对比SOA的Rpc)
>
> 真正的微服务,理论应该一个服务一个repo,没有库这种文件上的依赖,互相之间的调用走纯的RESTful API.也就是说服务之间的依赖仅仅是一纸契约,而这个契约的改动是很不频繁的.例如服务A和B,B依赖A.A是不需要提供库的,当B需要使用A的接口时,它可以直接调用RESTful API,也可以把这个REST API封装成某个库,但这也只是发生在A这一测.这样的好处是:只要B提供的服务契约不变,即便内部实现改变,A也无需改变.A和B完全可以独立开发,用不一样的语言不一样的框架等等
>
> dubbo这种就不是真正意义上的微服务,服务与服务之间存在代码依赖,绑的很死.所以dandang扩展的dubbox一个主要改变就是支持REST,把这种基于代码级别的依赖打破.
>
> SOA拆分粒度较大,一般按业务域划分,但比较少涉及系统内细粒度的拆分,需要集中的调度总线,易产生性能瓶颈

#### 优点

- 一组小服务
- 独立进程部署
- 轻量级通信 

#### 缺点

- 分布式复杂性
- 数据一致性
- 运维复杂性
- 测试复杂性 

#### 康威定律

- 时间再多一件事情也不可能做的完美，但总有时间做完一件事件

#### How to split?

>保证业务的独立性与完整性,围绕业务模块来进行拆分,但很多时候围绕业务拆分可能是一种理想下的拆分,微服务的便利更多在于团队内部产生闭环,服务间互相协调配合来为用户产生价值,每个服务都围绕着具体业务进行构建.**微服务**强调的是服务大小,而**微服务架构**是一种思想,需从整体上对软件系统进行通盘考虑

#### Reactive Microsystem

> 事件驱动的响应式微系统架构 

* 单体(Monolith)
  
> 一个实例集成了一个系统的所有功能

* 微单体(Micoliths)

  > 微单体介于单体跟微服务之间,有很多服务,数据也进行了拆分,不像单体那样只有一个WAR包和一个库,但又不像微服务那样每个服务有自己的库互不干涉

* 微服务(Microservice)

  > 每个服务都是一个微型应用,有着自己的六边型架构,包括商业逻辑
  >
  > 是对SOA思想的延续及发展,以服务为单位,拆分粒度更细,去中心化,不需要ESB  
  >
  > 有效拆分,实现敏捷开发和部署,可以使用不同编程语言 

#### 其它

- 专注于单一服务/功能的小型单元块为基础，利用模块化的方式组合成复杂的大型应用服务
- 在编程的世界中，最重要的便是**抽象**能力。
- 微服务改造的过程实际上也是个**抽象**的过程。
- 有一种抽象是把公共逻辑的方法做成公共的库，从而减少服务调用的性能损耗，但这种方法管理成本比较高，很难保证所有应用版本的一致性。
- 还有一种抽象是将这些抽象到一个反向代理组件，每个服务都额外部署这个代理组件，所有出入站的流量通过改组件进行处理跟转发，此组件被称为**Sidecar**。
- 单体向微服务改造的拆分中，也会引入新的问题
  - 故障跟踪 
  - 稳定性下降，服务数量变多导致其中一个服务出现故障的概率增大
  - 部署/管理的工作量变大
  - 测试方面，几乎所有功能都会涉及多个服务，导致复杂度增加
- Service Mesh，相比微服务框架的优势在于不侵入代码，让代码更关注于业务逻辑 
  - 数据平面，负责网络通信，data plane
  - 控制平面，负责配置管理，control plane 
- 微服务不是架构演进的终点，往细走还有 Serverless，FaaS(Function as a Service)等方向
  - FaaS简单理解为功能服务化，提供了一种比微服务更加服务碎片化的软件架构范式

