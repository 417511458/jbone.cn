---
title: 嵌入式Zuul反向代理
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，翻译
order: 3
---

Spring Cloud创建了一个嵌入式Zuul代理。以简化UI应用希望对一个或多个后端服务进行代理访问的常用案例的开发。这个特性对用户界面代理到所需的后端服务非常有用，从而避免了对所有后端独立管理CORS和身份认证核心问题的需要。

要启用这个代理服务，在Spring Boot主类上加上`@EnableZuulProxy`注解即可。加上之后会将本地调用转发到适当的服务。按照惯例，ID为`users`的服务接收来自`/users`（去掉前缀）代理的请求。代理服务使用`Ribbon`通过服务发现定位要转发的实例。所有请求都在`hystrix`命令中执行，所以故障会统计在`Hystrix`指标中。一旦打开断路器，代理服务就不会尝试与服务联系。

> Zuul启动器不包含服务发现客户端，所以，对于基于服务ID的路由，您还需要提供一个服务发现客户端（Eureka是一种选择）。

要跳过自动添加服务的过程，需要将忽略的服务ID匹配模式集合设置到`zuul.ignored-services`中。服务如果匹配到忽略的模式，但在路由关系图中明确配置了该服务，该服务将不会被忽略，如下例所示：

**application.yml.** 

```yaml
 zuul:
  ignoredServices: '*'
  routes:
    users: /myusers/**
```

上面的示例中，**除了**`users`，其他的都将会被忽略。

要增加或更改代理路由，您可以添加外部配置，如下所示：

**application.yml.** 

```yaml
 zuul:
  routes:
    users: /myusers/**
```

上面示例的含义是`/myusers`的HTTP调用会被转发的`users`服务（如`/myusers/101`会被转发到`/101`）。

要更细粒度的控制路由，您可以分别指定`path`和`serviceId`，如下所示：

**application.yml.** 

```yaml
 zuul:
  routes:
    users:
      path: /myusers/**
      serviceId: users_service
```

上面示例的含义是`myusers`的HTTP调用会被转发到`users_service`服务。路由必须具有一个可以指定为`ant-style`模式的`path`，因此`/myusers/*`仅匹配一级，而`/myusers/**`则是多级匹配。

后端地址可以指定为`serviceId`（针对来自服务发现的服务），也可以指定为`url`（针对物理位置），如下例所示：

**application.yml.** 

```yaml
 zuul:
  routes:
    users:
      path: /myusers/**
      url: https://example.com/users_service
```

这些简单的`url-routes`不会以`HystrixCommand`的方式执行，也不会使用`Ribbon`对多个URLs做负载均衡。为了实现这个目标，您可以指定一个静态服务列表的`serviceId`，如下所示：

**application.yml.** 

```yaml
zuul:
  routes:
    echo:
      path: /myusers/**
      serviceId: myusers-service
      stripPrefix: true

hystrix:
  command:
    myusers-service:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: ...

myusers-service:
  ribbon:
    NIWSServerListClassName: com.netflix.loadbalancer.ConfigurationBasedServerList
    listOfServers: https://example1.com,http://example2.com
    ConnectTimeout: 1000
    ReadTimeout: 3000
    MaxTotalHttpConnections: 500
    MaxConnectionsPerHost: 100
```




