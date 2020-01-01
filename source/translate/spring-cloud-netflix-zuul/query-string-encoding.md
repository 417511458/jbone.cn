---
title: 10.查询字符串编码
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Query String Encoding
order: 10
---

在处理传入的请求时，会对查询参数进行解码，以便它们可以在`Zuul`过滤器中进行修改。然后将它们重新编码，在路由过滤器中重建后端请求。如果（例如）使用Javascript的`encodeURIComponent()`方法对其进行编码，结果可能和原始输入不同。尽管这在大多数情况下不会有什么问题，但有些web服务器对复杂查询字符串的编码很挑剔。

要强制查询字符串的原始编码，可以设置`ZuulProperties`的特殊标志，以便使用`HttpServletRequest :: getQueryString`方法按原样获取查询字符串，如下例所示：

**application.yml.**

```yaml
 zuul:
  forceOriginalQueryStringEncoding: true
```

> 这个特殊标志只适用于`SimpleHostRoutingFilter`。同时，您失去了使用`RequestContext.getCurrentContext().setRequestQueryParams(someOverriddenParameters)`轻松覆盖查询参数的能力。因为此时的查询字符串是直接从原始`HttpServletRequest`中获取的。


