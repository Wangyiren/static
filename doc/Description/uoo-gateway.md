Spring Cloud Netflix Zuul是由Netflix开源的API网关，在微服务架构下，网关作为对外的门户，实现动态路由、监控、授权、安全、调度等功能。
Zuul基于servlet 2.5（使用3.x），使用阻塞API。 它不支持任何长连接，如websockets。而Gateway建立在Spring Framework 5，Project Reactor和Spring Boot 2之上，使用非阻塞API。 比较完美地支持异步非阻塞编程，先前的Spring系大多是同步阻塞的编程模式，使用thread-per-request处理模型。即使在Spring MVC Controller方法上加@Async注解或返回DeferredResult、Callable类型的结果，其实仍只是把方法的同步调用封装成执行任务放到线程池的任务队列中，还是thread-per-request模型。Gateway 中Websockets得到支持，并且由于它与Spring紧密集成，所以将会是一个更好的开发体验。

在一个微服务集成的项目中microservice-integration，我们整合了包括网关、auth权限服务和backend服务。提供了一套微服务架构下，网关服务路由、鉴权和授权认证的项目案例。

整个项目的架构图如下：

## Zuul网关
Zuul网关的主要功能为路由转发、鉴权授权和安全访问等功能。

Zuul中，很容易配置动态路由转发，如：
~~~
zuul:
    ribbon:
        eager-load:
        enabled: true     #zuul饥饿加载
    host:
        maxTotalConnections: 200
        maxPerRouteConnections: 20
    routes:
        user:
        path: /user/**
            ignoredPatterns: /consul
            serviceId: user
            sensitiveHeaders: Cookie,Set-Cookie
~~~~
默认情况下，Zuul在请求路由时，会过滤HTTP请求头信息中的一些敏感信息。这里不做介绍。

网关中还配置了请求的鉴权，结合Auth服务，通过Zuul自带的Pre过滤器可以实现该功能。当然还可以利用Post过滤器对请求结果进行适配和修改等操作。

## 迁移到Spring Cloud Gateway
实现的主要功能如下：路由转发、权重路由、断路器、限流、鉴权和黑白名单等。本文基于主要实现如下的三方面功能：
- 路由断言
- 过滤器（包括全局过滤器，如断路器、限流等）
- 全局鉴权
- 路由配置
- CORS

## 依赖
本文采用的Spring Cloud Gateway版本为2.0.0.RELEASE。增加的主要依赖如下，具体的细节可以参见Github上的项目。
