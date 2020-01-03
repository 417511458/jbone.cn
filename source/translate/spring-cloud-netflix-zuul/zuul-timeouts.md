---
title: 15.Zuul Timeouts
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Zuul Timeouts
order: 15
---

如果您要为Zuul代理的请求配置`socket`超时时间和`read`超时时间，根据您的配置有以下两个选择：

* 如果Zuul使用服务发现，您需要使用Ribbon的属性`ribbon.ReadTimeout`和`ribbon.SocketTimeout`来配置这些超时时间。
* 如果您通过指定URLs来配置Zuul路由，您需要使用`zuul.host.connect-timeout-millis`和`zuul.host.socket-timeout-millis`。
