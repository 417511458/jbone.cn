---
title: 概述
date: 2019-11-29 08:49:43
type: translate-cas
description: CAS服务管理概述
keywords: CAS服务管理，官方文档，翻译
order: 1
---

CAS服务管理设施可让CAS服务管理员声明和配置服务（CAS客户端）使用CAS的方式。服务管理设施的核心组件是服务注册中心，它存储一个或多个已注册的服务，这些已注册的服务包含驱动CAS行为的元数据：

* [授权服务](services-access-strategy.html) - 控制哪些服务可以参与CAS SSO会话。
* 强制身份认证 - 为强制身份认证提供管理控制。
* 属性发布 - 为服务的授权和个性化设置提供用户详情。
* [代理控制](services-proxy-policy.html) - 通过授予/拒绝代理身份认证功能，进一步控制授权的服务。
* 皮肤控制 - 为特定服务定义备用的CAS皮肤。

相关CAS属性配置，[请看这里](https://apereo.github.io/cas/6.0.x/configuration/Configuration-Properties.html#service-registry)。

## 服务管理web系统
服务管理webapp是一个独立的web应用程序，它提供了管理服务注册中心数据的GUI，可以独立于CAS部署。web管理系统必须和CAS server共享相同的注册中心配置，以便所有系统都可以加载相同的服务数据。想了解更多关于管理webapp的信息，[请看这里](../cas-management/overview.html)。

## 注册服务
注册服务提供了以下元数据：

字段 | 描述
--- | --- 
`id` | 必填的唯一标识符。这必须是一个有效的数字。
`name` | 必填的名字（少于等于255个字符）。
`description` | 服务的可选自由文本描述。（少于等于255个字符）。
`informationUrl` | 指向服务信息指南的可选自由文本链接。
`privacyUrl` | 指向服务隐私策略的可选自由文本链接。

