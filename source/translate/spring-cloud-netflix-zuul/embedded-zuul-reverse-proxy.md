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




