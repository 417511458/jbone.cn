---
title: 忽略头信息
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，翻译
order: 7
---

除了路由敏感头信息之外，您还可以通过`zuul.ignoredHeaders`来设置全局的和下游服务交互期间（请求和响应）需要丢弃的值。默认情况下，如果不使用`Spring Security`，那么这些值都是空的。否则，它们将初始化为由`Spring Security`指定的众所周知的`security`头信息（例如，涉及缓存）。在本案例中，假定下游服务也可能添加这些头信息，但是我们希望从代理中获取。要避免使用`Spring Security`时丢弃这些众所周知的`security`头信息，您可以将`zuul.ignoreSecurityHeaders`设置为`false`。如果您在`Spring Security`中禁用了`HTTP Security`响应头，并且希望下游服务提供该值，这样做非常有用。
