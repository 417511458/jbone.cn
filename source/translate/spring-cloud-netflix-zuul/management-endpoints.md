---
title: 7.管理端点
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，management endpoints
order: 7
---

默认情况下，如果你将`@EnableZuulProxy`和`Spring Boot Actuator`结合使用。则会启用两个附加端点:

* Routes
* Filters

> 译者注:
> 必须是和`Spring Boot Actuator`结合使用才会启用这两个管理端点，且访问路径的`contextPath`和`Spring Boot Actuator`的`contextPath`一致。

## Routes 端点

`/routes`端点的`GET`请求返回路由映射集合：

**GET /routes.**

```json
{
  /stores/**: "http://localhost:8081"
}
```

通过给`/routes`端点添加`?format=details`查询字符串，可以访问路由详情。加上之后会输出以下内容：

**GET /routes/details.**

```json
{
  "/stores/**": {
    "id": "stores",
    "fullPath": "/stores/**",
    "location": "http://localhost:8081",
    "path": "/**",
    "prefix": "/stores",
    "retryable": false,
    "customSensitiveHeaders": false,
    "prefixStripped": true
  }
}
```

`/routes`的`POST`请求强制刷新现有路由（例如，当服务目录发生变更时）。您可以将`endpoints.routes.enabled`设置为`false`来禁用该端点。

> 路由应该自动响应服务目录的变更，但对`/routes`进行`POST`请求是一种强制立即变更的方式。

## Filters 端点

`/filters`端点的`GET`请求按过滤器类型返回Zuul过滤器的map集合。对于map中的每种过滤器类型，都将获取到该类型的所有过滤器及其详情。


