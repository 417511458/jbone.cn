---
title: 5.Cookies和敏感头信息
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，翻译
order: 5
---

您可以在同一系统中的服务间共享头信息，但是您可能不想让敏感头信息泄露到下游外部服务。您可以在路由配置部分指定要忽略的头信息集合。`Cookies`发挥着重要的作用，因为它们在浏览器中具有定义明确的语义，并且它们始终是被认为敏感的。如果您代理的使用者是浏览器，那么下游服务的cookies也会给用户带来问题，因为它们都混在一起了（所有下游服务看起来都像是来自同一个地方）。

如果您对服务的设计很谨慎，（例如，如果只有一个下游服务设置cookies），您可以让它们从后端一直透传到调用者。另外，如果您的代理设置了cookies，并且您的所有后端服务都属于同一系统的一部分，那么很自然的就可以简单的共享它们（并且，例如，使用`Spring Session`将它们连接到一些共享状态）。除此之外，下游服务设置的cookies对于调用者都没用，所以建议您（至少）将`Set-Cookie`和`Cookie`设置为不属于您域的路由的敏感头信息。即使对于属于您域的路由，在让cookies在它们和代理之间流动之前，也请尝试仔细思考其含义。（译者注：这里的`它们`是指路由和下游服务，`代理`指浏览器）

可以以逗号分隔的形式配置每个路由的敏感头信息集合，如下例所示：

**application.yml.**

```yaml
 zuul:
  routes:
    users:
      path: /myusers/**
      sensitiveHeaders: Cookie,Set-Cookie,Authorization
      url: https://downstream
```

> 以上是`sensitiveHeaders`的默认值，因此如果您不想改变这个配置，则不需要设置。这是Spring Clound Netflix 1.1中的新特性（1.0中，用户不能控制头信息，并且所有cookies都是双向流动的）。

`sensitiveHeaders`是一个黑名单，且默认值非空。因此，要使Zuul传递所有头信息（被忽略的除外），您必须明确的将该配置设置为空。如果您想将cookie或授权头信息传递到后端，则必须要这么做。下面的示例展示了如何使用`sensitiveHeaders`：

**application.yml.**

```yaml
 zuul:
  routes:
    users:
      path: /myusers/**
      sensitiveHeaders:
      url: https://downstream
```

您还可以通过`zuul.sensitiveHeaders`设置敏感头信息。如果在路由上设置了`sensitiveHeaders`，路由上的`sensitiveHeaders`将会覆盖全局的`sensitiveHeaders`。



