
# 使用前参考文档
- [Spring Cloud参考资料](https://github.com/rothschil/static/tree/master/doc/Description/spring-cloud.md)
- [Spring Cloud学习源码](https://github.com/rothschil/spring-cloud.git)
- [关于微服务模块的技术选型](https://github.com/rothschil/static/tree/master/doc/Description/Selection.md)
- [关于人员编码规则说明](https://github.com/rothschil/static/tree/master/doc/Description/Rule.md)

# 用户组织运营平台 UOO
## 项目简介
uoo是采用Spring Cloud微服务化开发平台，具有统一授权、认证后台管理系统，也可以作为后端服务的开发脚手架。代码要求简洁、架构清晰，适合对微服务学习和直接项目中使用。核心技术采用Spring Boot 1.5.15.RELEASE以及Spring Cloud (1.4.5.RELEASE)相关核心组件，当下前端采用Thymeleaf的HTML来开发的，后续准备采用VUE进行二次改造。

## 模块职责划分
### uoo-base 基类
- base-common 基类通用，可对工具类和对底层访问的Dao进行封装，建议提供基于不同数据库和不同持久化工具进行分装，供各个模块自行引用
- uoo-register Eureka注册中心,建议最少三个节点部署，分三台机器
- uoo-gateway 统一配置网关服务，提供对外路由的配置
- uoo-sleuth 基于Zipkin来对服务链路追踪分析,可以可视化地分析服务调用链路和服务间的依赖关系
- uoo-monitor 微服务监控中心
### uoo-common 平台通用
- common-system 提供基于平台的用户、部门、角色、字典的管理
- activiti-core 工作流的基本服务,目前上面仅有内蒙项目组的代码
- common-cache 提供统一的基于缓存中间的服务，这块规划采用redis集群构建缓存中间服务
- queues-manager 基于RabbitMQ对外统一提供队列的基本管理服务功能
- common-im 提供基于短信、邮件、代办的集中服务功能模块，方便后期版本升级迭代

### uoo-business 业务领域
- business-syn-msg 对外数据共享同步的服务模块
- business-organization 组织机构相关业务服务，包括组织分类、组织关系、组织树等
- business-personnel 人员信息相关业务服务，包括证件、教育经理、履历等
- business-user 用户相关业务，包括账号、从账号、账号与组织归属关系
- business-permission 权限业务领域模块，包括角色、资源等
- business-position 岗位职位业务领域，当前岗位、职位的定义还较为欠缺，需要补充
- business-region 区域管理业务领域
- business-authentication 认证鉴权所用的业务，可对认证管理WEB提供服务

## uoo-common通用部分
### common-system
详细内容请参考[common-system说明](https://github.com/rothschil/static/tree/master/doc/Description/common-system.md)

### uoo-gateway
参考[uoo-gateway说明](https://github.com/rothschil/static/tree/master/doc/Description/uoo-gateway.md)

## 开发必读
- 环境准备： JDK1.8、Maven3+、IDEA
- 内存推荐：4G+
- 下载源码：[git@wongs.xyz:uoo.git](git@wongs.xyz:uoo.git)


## 微服务模块化结构

~~~
|——uoo----------------------------------------------------父项目，公共依赖
|   |——uoo-base-------------------------------------------基础模块
|   |   |——base-common------------------------------------基础部分
|   |   |   |——common-tool--------------------------------工具脚手架类
|   |   |   |——common-abstract----------------------------通用持久层，、可复用配置包
|   |   |   |——common-persitence--------------------------通用持久层
|   |   |   |   |——persistence-jpa------------------------JPA实现类
|   |   |——uoo-register-----------------------------------注册中心
|   |   |——uoo-gateway------------------------------------网关中心
|   |   |——uoo-sleuth-------------------------------------链路追踪中心
|   |   |——uoo-monitor------------------------------------监控中心
|   |——uoo-common-----------------------------------------公共模块，平台自身
|   |   |——common-system----------------------------------平台用户、部门等
|   |   |——common-activiti--------------------------------平台工作流
|   |   |   |——activiti-core------------------------------工作流模块，目前仅内蒙项目
|   |   |——common-cache-----------------------------------平台缓存服务中心
|   |   |——common-queues----------------------------------队列管理服务中心
|   |   |   |——queues-manager-----------------------------rabbitMQ队列管理能力
|   |   |——common-im--------------------------------------实时通讯服务中心
|   |——uoo-business---------------------------------------UOO核心模块
|   |   |——business-syn-msg-------------------------------数据共享组件服务中心
|   |   |——business-organization--------------------------组织域服务中心
|   |   |——business-personnel-----------------------------人员域服务中心
|   |   |——business-user----------------------------------用户域服务中心
|   |   |——business-permission----------------------------权限域服务中心
|   |   |——business-position------------------------------职位域服务中心
|   |   |——business-region--------------------------------区域服务中心
|   |   |——business-public--------------------------------公共服务中心，目前被uoo用做系统管理
|   |   |——business-authentication------------------------认证服务中心
|   |——uoo-business-api-----------------------------------UOO开放式服务API
|   |   |——api-user---------------------------------------用户域开放式API
|   |   |——api-org----------------------------------------组织域开放式API
|   |   |——api-security-----------------------------------安全域开放式API
|   |   |   |——security-authorize-------------------------授权管理服务API
|   |   |   |——security-authentication--------------------身份认证服务API
|   |   |   |   |——authentication-cs----------------------身份认证服务API-CS方式
|   |   |   |   |——authentication-bs----------------------身份认证服务API-BS方式
|   |——uoo-business-web-----------------------------------UOO WEB端
|   |   |——mdm-web----------------------------------------主数据中心维护支撑WEB
|   |   |——auth-web---------------------------------------认证中心维护支撑WEB

~~~~

## 部署发布
- [机器部署列表](https://github.com/rothschil/static/tree/master/doc/Description/deploy.md)