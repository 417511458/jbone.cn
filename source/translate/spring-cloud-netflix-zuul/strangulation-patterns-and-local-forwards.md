---
title: 8.抑制模式和本地转发
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Strangulation Patterns and Local Forwards
order: 8
---

当迁移现有应用或API时，常用方式是`抑制`老端点，慢慢地将这些老端点替换成不同的实现。对于这种场景，Zuul代理是一个非常有用的工具，因为您可以使用它去处理来自老端点的所有流量，并将其中的一些请求重定向到新端点。

以下示例展示了`抑制`方案的配置详情：

**application.yml.**

```yaml
 zuul:
  routes:
    first:
      path: /first/**
      url: https://first.example.com
    second:
      path: /second/**
      url: forward:/second
    third:
      path: /third/**
      url: forward:/3rd
    legacy:
      path: /**
      url: https://legacy.example.com
```

以上示例中，我们在`抑制``legacy`服务，它处理其他所有服务匹配不上的请求。`/first/**`中的路径被提取到具有外部URL的新服务中。`/second/**`中的路径被转发，以便它们可以在本地处理（例如，使用普通Spring的`@RequestMapping`）。`/third/**`中的路径被转发到不同前缀的服务（`/third/foo`被转发到`/3rd/foo`）。

> 被忽略的匹配模式不是完全忽略，它们只是不会由代理处理（因此它们还是在本地有效转发）。

