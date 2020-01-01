---
title: 9.通过Zuul上传文件
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Uploading Files through Zuul
order: 9
---

如果您使用`@EnableZuulProxy`，只要是小文件，您就可以使用代理路径上传文件。而对于大文件，`/zuul/*`中有一个替代路径可以绕过`Spring DispatcherServlet`（避免多部分处理）。换句话说，如果您设置了`zuul.routes.customers=/customers/**`，那么您可以将大文件`POST`到`/zuul/customers/*`。servlet路径通过`zuul.servletPath`外部化。如果代理路由通过`Ribbon`负载均衡，那么大文件也要提高超时配置，如下例所示：

**application.yml.**

```yaml
hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds: 60000
ribbon:
  ConnectTimeout: 3000
  ReadTimeout: 60000
```

请注意，对于流式处理大文件，您需要在请求中使用分块编码（某些浏览器默认情况下不使用）。如下例所示：

```shell script
$ curl -v -H "Transfer-Encoding: chunked" \
    -F "file=@mylarge.iso" localhost:9999/zuul/simple/file
```

