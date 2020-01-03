---
title: 12.纯嵌入式Zuul
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Plain Embedded Zuul
order: 12
---

如果您使用`@EnableZuulServer`(而不是`@EnableZuulProxy`)，您还可以在不进行代理的情况下运行Zuul服务器，或者有选择性的打开代理平台的某些部分。您添加到应用的所有ZuulFilter类型的bean都会自动安装（就像`@EnableZuulProxy`一样），但不会自动添加代理过滤器。

在这种情况下，仍然使用`zuul.routes.*`配置来定义Zuul服务中的路由，但因为没有服务发现和代理。所以`serviceId`和`url`配置都会被以忽略。以下示例将`/api/**`的所有路径映射到Zuul过滤器链：

**application.yml.**

```yaml
 zuul:
  routes:
    api: /api/**
```

