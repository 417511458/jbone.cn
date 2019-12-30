---
title: 1. 概述
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，翻译
order: 1
---

路由是微服务架构中不可缺少的一个组成部分。例如，`/`可能映射到您的web应用、`/api/users/`映射到用户服务、以及`/api/shop`映射到店铺服务。Zuul是来自Netflix的基于JVM的路由器和服务端负载均衡器。

Netflix使用Zuul做以下事情：

* 身份认证

* 洞察（Insights）  _不知道有没有更好的翻译，待优化_

* 压力测试

* 金丝雀测试（Canary Testing）

* 动态路由

* 服务迁移

* 降载

* 安全

* 静态响应处理

* 主动流量管理

Zuul的规则引擎可以用任何JVM语言来编写规则和过滤器，并内置支持Java和Groovy。

> `zuul.max.host.connections`的配置已经替换成了`zuul.host.maxTotalConnections`和`zuul.host.maxPerRouteConnections`，默认值分别是`200`和`20`。

> 所有路由的默认Hystrix隔离模式（`ExecutionIsolationStrategy`）是`SEMAPHORE`。如果隔离模式首选`THREAD`，则可以将`zuul.ribbonIsolationStrategy`改为`THREAD`。
