
## 服务中心部署

### 多环境配置Shell脚本部署

#### 说明
实际开发过程中，开发环境、测试环境、生产环境的配置是不一样的，spring boot定义的规则是允许按照命名方式不同来激活响应的配置文件。
如在启动过程中，根据 “--spring.profiles.active={自己定义}”，分别激活响应的配置

![avatar](https://github.com/rothschil/static/raw/master/Snipaste_2018-12-06_18-04-13.jpg)
要求 application.yml/application.properties具备多个spring.profiles.active，用以区分环境，案例如下：
~~~
spring:
  profiles: 221
  application:
    name: uoo-register
eureka:
  instance:
    prefer-ip-address: true
    hostname: 134.96.253.221
  client:
    serviceUrl:
      defaultZone: http://134.96.253.222:8761/eureka/,http://134.96.253.223:8761/eureka/
server:
  port: 8761

---
spring:
  profiles: 222
  application:
    name: uoo-register
eureka:
  instance:
    prefer-ip-address: true
    hostname: 134.96.253.222
  client:
    serviceUrl:
      defaultZone: http://134.96.253.221:8761/eureka/,http://134.96.253.223:8761/eureka/
server:
  port: 8761

---
spring:
  profiles: 223
  application:
    name: uoo-register
eureka:
  instance:
    prefer-ip-address: true
    hostname: 134.96.253.223
  client:
    serviceUrl:
      defaultZone: http://134.96.253.221:8761/eureka/,http://134.96.253.222:8761/eureka/
server:
  port: 8761
~~~

#### shell脚本

[spring-boot源码](https://github.com/rothschil/static/raw/master/spring-boot.sh)


    sh spring-boot.sh start YOUR_APP_NAME.jar --spring.profiles.active={自己定义}


- 第一个参数: start为启动命令 (也可以为,stop,restart,status)
- 第二个参数: YOUR_APP_NAME.jar为需启动的jar包
- 第三个参数: --spring.profiles.active 启动参数(为空则使用激活默认，加参数的条件下，则激活相应的配置，如”--spring.profiles.active=223“，则激活spring.profiles名字为223)

~~~

function start()
{
    count='ps -ef |grep java|grep $SpringBoot|grep -v grep|wc -l'
    
    if [[ $count -eq 0 ]];
    then
        echo "$SpringBoot is running..."
    else
        echo "Start $SpringBoot success..."
        nohup java -jar $SpringBoot  $START_OPTS > /dev/null 2>&1 &
    fi
}
~~~

#### 执行
- 253.221机器上执行
    
    sh spring-boot.sh start uoo-register-1.0.1.RELEASE.jar --spring.profiles.active=221
- 253.222机器上执行
    
    sh spring-boot.sh start uoo-register-1.0.1.RELEASE.jar --spring.profiles.active=222
- 253.223机器上执行
    
    sh spring-boot.sh start uoo-register-1.0.1.RELEASE.jar --spring.profiles.active=223

### 测试环境

### 地址
测试环境资源有限情况下，每个服务可在每台机器部署一个节点

![avatar](https://github.com/rothschil/static/raw/master/tuop.png)

| 序号 | 地址 | 描述 |
| ------ | ------ | ------ |
| 1 | 134.96.253.221 | 无 |
| 2 | 134.96.253.222 | 无 |
| 3 | 134.96.253.223 | 无 |

#### uoo-base基础模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ 
| 86 | uoo-sleuth | 链路追踪中心 | 8600 | 8611、8622 |
| 87 | uoo-register | 注册中心 | 8751 | 8761、8771 |
| 88 | uoo-gateway | 通用网关 | 8800 | 8811、8822 |
| 89 | uoo-monitor | 监控中心 | 8900 | 8911、8922 |

#### uoo-common公用模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 91 | common-system | 平台用户、部门等 | 9100 | 9111、9122 |
| 92 | activiti-core | 工作流模块 | 9200 | 9211、9222 |
| 93 | common-cache | 平台缓存服务中心 | 9300 | 9311、9322 |
| 94 | queues-manager | 队列管理能力 | 9400 |9411、9422 |
| 95 | common-im | 实时通讯服务中心 | 9500 | 9511、9522 |

#### uoo-business UOO核心模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 110 | business-syn-msg | 数据共享组件服务中心 | 11000 | 11022、11033 |
| 111 | business-organization |组织域服务中心 | 11100 | 11111、11122 |
| 112 | business-personnel | 人员域服务中心 | 11200 | 11211、11222 |
| 113 | business-user | 用户业务服务中心 | 11300 | 11311、11322 |
| 114 | business-permission | 权限域服务中心 | 11400 | 11411、11422 |
| 115 | business-public | 公共域服务中心 | 11500 | 11511、11522 |
| 116 | business-position | 职位域服务中心 | 11600 | 11611、11622 |
| 117 | business-region | 区域服务中心 | 11700 | 11711、11722 |
| 118 | business-authentication | 认证服务中心 | 11800 | 11811、11822 |

#### uoo-business-web业务模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 180 | mdm-web | 主数据中心维护支撑WEB | 18000 | 18022、18033 |
| 181 | auth-web | 认证中心维护支撑WEB | 18100 | 18111、18122 |

#### uoo-business-api业务模块

| 序号 | 名称 | 描述 |  阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 200 | api-user | 用户域开放式API | 20000 | 20022、20033 |
| 201 | api-org | 组织域开放式API | 20100 | 20111、20122 |
| 202 | security-authorize | 授权管理服务API | 20200 | 20211、20222 |
| 203 | authentication-bs | 身份认证服务API-BS方式 | 20300 | 20311、20322 |
| 204 | authentication-cs | 身份认证服务API-CS方式 | 20400 | 20411、20422 |

### 正式生产环境
#### uoo-base基础模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 86 | uoo-sleuth | 链路追踪中心 | 8600、8611 | 8622、8633、8644 |
| 87 | uoo-register | 注册中心 | 8751、8761 | 8771，8781、8791 |
| 88 | uoo-gateway | 通用网关 | 8800、8811 | 8822、8833、8444 |
| 89 | uoo-monitor | 监控中心 | 8900、8911 | 8922、8933、8944 |

#### uoo-common公用模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 91 | common-system | 平台用户、部门等 | 9100、9111 | 9122、9133、9144 |
| 92 | activiti-core | 工作流模块 | 9200、9211 | 9222、9233、9244 |
| 93 | common-cache | 平台缓存服务中心 | 9300、9311 | 9322、9333、9344 |
| 94 | queues-manager | 队列管理能力 | 9400、9411 | 9422、9433、9444 |
| 95 | common-im | 实时通讯服务中心 | 9500、9511 | 9522、9533、9544 |

#### uoo-business UOO核心模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 110 | business-syn-msg | 数据共享组件服务中心 | 11000、11022 | 11033、11044、11055 |
| 111 | business-organization |组织域服务中心 | 11100、11111 | 11122、11133、11144 |
| 112 | business-personnel | 人员域服务中心 | 11200、11211 | 11222、11233、11244 |
| 113 | business-user | 用户域服务中心 | 11300、11311 | 11322、11333、11344 |
| 114 | business-permission | 权限域服务中心 | 11400、11411 | 11422、11433、11444 |
| 115 | business-public | 公共域服务中心 | 11500 | 11511、 11522、11533、11544 |
| 116 | business-position | 职位域服务中心 | 11600 | 11611、11622、11633、11644 |
| 117 | business-region | 区域服务中心 | 11700 | 11711、11722、11733、11744 |
| 118 | business-authentication | 认证服务中心 | 11800 | 11811、11822、11833、11844 |

#### uoo-business-web业务模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 180 | mdm-web | 主数据中心维护支撑WEB | 18000 | 18011、18022、18033、18044 |
| 181 | auth-web | 认证中心维护支撑WEB | 18100 | 18111、18122、18133、18144 |

#### uoo-business-api业务模块

| 序号 | 名称 | 描述 | 阶段I部署端口 | 阶段II部署端口 |
| ------ | ------ | ------ | ------ | ------ |
| 200 | api-user | 用户域开放式API | 20000、20022 | 20033、20033、20044  |
| 201 | api-org | 组织域开放式API | 20100、20111 | 20122、20133、20144 |
| 202 | security-authorize | 授权管理服务API | 20200、20211 | 20222、20233 、20244  |
| 203 | authentication-bs | 身份管理服务API-BS方式 | 20300、20311 | 20322、20333、20344 |
| 204 | authentication-cs | 身份管理服务API-CS方式 | 20400、20411 | 20422、20433、20444 |

### 详细部署清单

![avatar](https://github.com/rothschil/static/raw/master/Snipaste_2018-12-04_21-22-03.jpg)

