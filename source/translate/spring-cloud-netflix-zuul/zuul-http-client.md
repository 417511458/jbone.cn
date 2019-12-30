---
title: 4.Zuul Http Client
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，翻译
order: 4
---

Zuul使用的默认HTTP客户端现在是`Apache HTTP Client`，而不是弃用的`Ribbon RestClient`。想要使用`RestClient`或`okhttp3.OkHttpClient`，需要分别设置`ribbon.restclient.enabled=true`或`ribbon.okhttp.enabled=true`。如果您想要自定义`Apache HTTP client`或`OK HTTP client`，请提供`ClosableHttpClient`或`OkHttpClient`类型的bean。

