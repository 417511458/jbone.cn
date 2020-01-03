---
title: 13.禁用Zuul Filters
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Disable Zuul Filters
order: 13
---

Zuul为Spring Cloud附带了许多`ZuulFilter`beans，并且这些beans在`proxy`模式和`server`模式下都是默认启用的。在[Zuul过滤器包](https://github.com/spring-cloud/spring-cloud-netflix/tree/master/spring-cloud-netflix-zuul/src/main/java/org/springframework/cloud/netflix/zuul/filters)中查看您可以启用的过滤器集合。如果您想禁用某个过滤器，请设置`zuul.<SimpleClassName>.<filterType>.disable=true`。按照惯例，`filters`后面的包是Zuul过滤器的类型。例如，想禁用`org.springframework.cloud.netflix.zuul.filters.post.SendResponseFilter`，需要设置`zuul.SendResponseFilter.post.disable=true`。


