---
title: 8.抑制模式和本地转发
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Strangulation Patterns and Local Forwards
order: 8
---

当迁移现有应用或API时，常用方式是`抑制`老端点，慢慢地将这些老端点替换成不同的实现。对于该场景，Zuul代理是一个非常有用的工具，因为您可以使用它去处理来自老端点的所有流量，并将其中的一些请求重定向到新端点。





