### Envoy是什么？

Envoy 是一个面向服务架构的L7代理和通信总线而设计的，这个项目诞生是出于以下目标：<br />

_对于应用程序而言，网络应该是透明的，当发生网络和应用程序故障时，能够很容易定位出问题的根源。_<br />

实际上，实现前面提到的目标是非常困难的，Envoy 试图通过提供以下高级特性：<br />

**外置进程架构**：Envoy是一个独立的进程，与应用程序一起运行。所有的Envoy形成一个对应用透明的通信网格，每个应用程序通过本地发送和接受消息，并不感知网络拓扑结构。这个外置进程的架构相比传统的基于library库服务通信，有两个优势；<br />

- Envoy可以与任何语言开发的应用一起工作。Java, C++, Go, PHP, Python等都可以基于Envoy部署成一个服务网格，在面向服务的架构体系中，使用多语言开发应用越来越普遍，Envoy填补了这一空白。<br />

- 任何参与面向服务的大型架构里工作的人都知道，部署升级library是非常令人痛苦的，而现在可以在整个基础设施上快速升级Envoy。

**基于新C++11编码**：Envoy是基于C++11编写的，之所以这样选择，是因为我们认为，Envoy这样的体系结构组件能够快速有效的开发出来。而现代应用程序开发者已经很难在云环境部署和使用它；通常会选择性能不高，但是能够快速提升开发效率的PHP、Python、Ruby、Scala等语言，并且能够解决复杂的外部环境依赖。并不像本地开发代码那样使用如C、C++能够提供高效的性能。

**L3/L4过滤器**：Envoy其核心是一个L3/L4网络代理，能够作为一个可编程过滤器实现不同TCP代理任务，插入到主服务当中。通过编写过滤器来支持各种任务，如原始TCP代理、HTTP代理、TLS客户端证书身份验证等。

**HTTP L7过滤器**：在现代应用架构中，HTTP是非常关键的部件，Envoy支持一个额外的HTTP L7过滤层。HTTP过滤器作为一个插件，插入到HTTP链接管理子系统中，从而执行不同的任务，如缓冲，速率限制，路由/转发，嗅探Amazon的DynamoDB等等。

**支持HTTP/2**：在HTTP模式下，Envoy支持HTTP/1.1、HTTP/2，并且支持HTTP/1.1、HTTP/2双向代理。这意味着HTTP/1.1和HTTP/2，在客户机和目标服务器的任何组合都可以桥接。建议在服务间的配置使用时，Envoy之间采用HTTP/2来创建持久网络连接，这样请求和响应可以被多路复用。Envoy并不支持被淘汰SPDY协议。

**HTTP L7路由**：在HTTP模式下运行时，Envoy支持根据content type、runtime values等，基于path的路由和重定向。当服务构建到服务网格时，Envoy为前端/边缘代理，这个功能是非常有用的。

**支持gRPC**：gRPC是一个来自谷歌的RPC框架，使用HTTP/2作为底层的多路传输。HTTP/2承载的gRPC请求和应答，都可以使用Envoy的路由和LB能力。所以两个系统非常互补。

**支持MongoDB L7**：现代Web应用程序中，MongoDB是一个非常流行的数据库应用，Envoy支持获取统计和连接记录等信息。

**支持DynamoDB L7**：DynamoDB是亚马逊托管的NoSQL Key/Value存储。Envoy支持获取统计和连接等信息。

**服务发现**：服务发现是面向服务体系架构的重要组成部分。Envoy支持多种服务发现方法，包括异步DNS解析和通过REST请求服务发现服务。

**健康检查**：构建Envoy网格的推荐方法，是将服务发现视为一个最终一致的方法。Envoy含有一个健康检查子系统，它可以对上游服务集群进行主动的健康检查。然后，Envoy联合服务发现、健康检查信息来判定健康的LB对象。Envoy作为一个外置健康检查子系统，也支持被动健康检查。

**高级LB**：在分布式系统中，不同组件之间LB也是一个复杂的问题。Envoy是一个独立的代理进程，不是一个lib库，所以他能够在一个地方实现高级LB，并且能够被任何应用程序访问。目前，包括自动重试、断路器，全局限速，阻隔请求，异常检测。将来还会支持按计划进行请求速率控制。

**前端代理**：虽然Envoy作为服务间的通信系统而设计，但是（调试，管理、服务发现、LB算法等）同样可以适用于前端，Envoy提供足够的特性，能够作为绝大多数Web应用的前端代理，包括TLS、HTTP/1.1、HTTP/2，以及HTTP L7路由。

**极好的可观察性**：如上所述，Envoy目标是使得网络更加透明。而然，无论是网络层还是应用层，都可能会出现问题。Envoy包括对所有子系统，提供了可靠的统计能力。目前只支持statsd以及兼容的统计库，虽然支持另一种并不复杂。还可以通过管理端口查看统计信息，Envoy还支持第三方的分布式跟踪机制。

**动态配置**：Envoy提供分层的动态配置API，用户可以使用这些API构建复杂的集中管理部署。

### 设计目标

**简要说明**：Envoy并不是很慢（我们已经花了相当长的时间来优化关键路径）。基于模块化编码，易于测试，而不是性能最优。我们的观点是，在其他语言或者运行效率低很多的系统中，部署和使用Envoy能够带来很好的运行效率。


### 返回
- [简介](../Introduction.md)
- [首页目录](../README.md)