
## 架构概述
### 服务注册发现
基于Eureka来实现的服务注册与调用，在Spring Cloud中使用Feign, 我们可以做到使用HTTP请求远程服务时能与调用本地方法一样的编码体验，开发者完全感知不到这是远程方法，更感知不到这是个HTTP请求。

### 服务鉴权
通过JWT的方式来加强服务之间调度的权限验证，保证内部服务的安全性

### 监控
利用Zipkin对服务链路的监控和分析

### 熔断机制
因为采取了服务的分布，为了避免服务之间的调用“雪崩”，采用了Hystrix的作为熔断器，避免了服务之间的“雪崩”。

### 负载均衡
将服务保留的Rest API进行代理和网关控制，除了平常经常使用的node.js、nginx外，Spring Cloud系列的zuul和ribbon，可以帮我们进行正常的网关管控和负载均衡。
其中扩展基于JWT的Zuul限流插件，方面进行限流。

## 技术原理概述
### SpringCloud调用机制

![avatar](https://github.com/rothschil/static/raw/master/images/1-springcloud.jpg)