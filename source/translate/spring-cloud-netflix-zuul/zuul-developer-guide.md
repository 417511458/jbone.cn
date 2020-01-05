---
title: 19.Zuul开发者指南
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Zuul Developer Guide
order: 19
---

有关Zuul工作原理的概述，请看[Zuul Wiki](https://github.com/Netflix/zuul/wiki/How-it-Works)。

## Zuul Servlet

Zuul是以Servlet实现的。对于一般情况，Zuul被嵌入到`Spring Dispatch`机制中。这使得Spring MVC可以控制路由。在这种情况下，Zuul缓冲请求。如果在经过Zuul时不需要缓冲请求（例如，上传大文件），Servlet也安装在Spring Dispatcher之外。默认情况下，这个servlet的地址是`/zuul`。可以通过`zuul.servlet-path`属性拉修改这个地址。

> 译者注：
> Zuul Servlet是一个公共实现，默认情况下，Spring Cloud会初始化两个入口，`ZuulController`和`ZuulServlet`。对于一般请求都是通过`ZuulController`；对于特殊需求（如上传大文件）可通过`ZuulServlet`。




