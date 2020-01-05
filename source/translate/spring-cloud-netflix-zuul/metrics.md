---
title: 18.Metrics
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Metrics
order: 18
---

Zuul会在Actuator metrics端点下，为路由请求时可能发生的故障提供度量指标。这些度量指标可以通过`/actuator/metrics`查看。这些度量指标的名字格式为`ZUUL::EXCEPTION:errorCause:statusCode`。

> 译者注：
> `/actuator/metrics`可以查看所有度量指标，其中包括Zuul的故障度量指标；
> 度量指标的名字示例：`ZUUL::EXCEPTION:post:SendResponseFilter:500`
