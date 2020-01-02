---
title: 11.请求URI编码
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Request URI Encoding
order: 11
---

在处理传入的请求时，会在匹配路由之前解码请求URI。然后在路由过滤器中重建后端请求时重新编码请求URI。如果您的URI包含已编码的`/`字符，可能会导致一些意想不到的行为。

想要使用原始的请求URI，可以设置`ZuulProperties`的特殊标志，以便使用`HttpServletRequest::getRequestURI`方法按原样获取URI，如下例所示：

```yaml
 zuul:
  decodeUrl: false
```

> 如果您正在使用`requestURI` RequestContext属性来覆盖请求URI，并且将该标志设置为`false`，那么将不会对请求上下文中设置的URL进行编码。您将要负责确保URL已被编码。

