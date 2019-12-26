---
title: 嵌入式Zuul反向代理
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，翻译
order: 3
---

Spring Cloud创建了一个嵌入式Zuul代理。以简化UI应用希望对一个或多个后端服务进行代理访问的常用案例的开发。这个特性对用户界面代理到所需的后端服务非常有用，从而避免了对所有后端独立管理CORS和身份认证核心问题的需要。

要启用这个代理服务，在Spring Boot主类上加上`@EnableZuulProxy`注解即可。加上之后会将本地调用转发到适当的服务。按照惯例，ID为`users`的服务接收来自`/users`（去掉前缀）代理的请求。代理服务使用`Ribbon`通过服务发现定位到要转发的实例。所有请求都在`hystrix`命令中执行，所以故障会统计在`Hystrix`指标中。一旦打开断路器，代理服务就不会尝试与服务联系。

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

另外一个方法是指定`service-route`并给`serviceId`配置`Ribbon`客户端（这样做需要禁用`Ribbon`中的`Eureka` - [了解更多](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#spring-cloud-ribbon-without-eureka)），如下例所示：

**application.yml.** 

```yaml
zuul:
  routes:
    users:
      path: /myusers/**
      serviceId: users

ribbon:
  eureka:
    enabled: false

users:
  ribbon:
    listOfServers: example.com,google.com
```

您可以使用`regexmapper`在`serviceId`和`routes`之间提供约定。它使用正则表达式命名组从`serviceId`提取变量，并将这些变量注入到路由匹配模式中，如下例所示：

**ApplicationConfiguration.java.**

```java
@Bean
public PatternServiceRouteMapper serviceRouteMapper() {
    return new PatternServiceRouteMapper(
        "(?<name>^.+)-(?<version>v.+$)",
        "${version}/${name}");
}
```

上面示例的含义是`myusers-v1`的`serviceId`被映射到路由`/v1/myusers/**`。这里可以接受任意正则表达式，但所有命名组必须同时出现在`servicePattern`和`routePattern`中。如果`servicePattern`不匹配`serviceId`，则使用默认行为。在上面的示例中，`myusers`的`serviceId`会被映射到`/myusers/**`路由（没检测到版本）。默认情况下这个功能是禁用的，且仅适用于服务发现的服务。

设置`zuul.prefix`的值，给所有映射加前缀，如`/api`。默认情况下，请求在转发之前，代理前缀将从请求中剥离（您可以通过`zuul.stripPrefix=false`关闭该功能）。您还可以关闭从单个路由中剥离特定于服务的前缀。如下例所示：

**application.yml.*

```yaml
 zuul:
  routes:
    users:
      path: /myusers/**
      stripPrefix: false
```

> `zuul.stripPrefix`仅适用于`zuul.prefix`中设置的前缀。它对给定路由`path`中定义的前缀没有任何影响。
  
上面的示例中，请求`/myusers/101`会被转发到`users`服务的`/myusers/101`。








