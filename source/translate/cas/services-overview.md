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
`logo` | 可选的，服务的logo图片路径。该图片会与`description`和`name`一起展示在登录页面。图片路径可以是CAS应用的图片目录的相对路径，也可以是完整的URL。
`serviceId` | 必填的，描述逻辑服务的正则表达式。逻辑服务定义了一个或多个服务所在的URL。url的匹配定义必须要格外小心，因为它可能造成安全漏洞。
`theme` | 可选的，当服务请求票据时，可以用于自定义CAS UI的主题名。[查看这里，了解更多](https://apereo.github.io/cas/6.0.x/ux/User-Interface-Customization.html)
`proxyPolicy` | 确定服务是否可以代理身份认证。[查看这里，了解更多](services-proxy-policy.html)。
`evaluationOrder` | 确定已注册服务的相对估值顺序。这个标记在两个服务的URL表达式覆盖相同服务的场景中尤其重要；估值顺序决定首先评估哪个注册信息，并充当内部排序因子。
`requiredHandlers` | 一组身份认证处理器名字，这些身份认证处理器必须成功的认证证书后才能访问服务。如果定义了身份认证处理器，就只选择选定的处理器来认证这个注册服务的请求。[查看这里，了解更多](services-required-auth.html)。
`attributeReleasePolicy` | 描述可发布到应用的属性集的策略。以及其他需要清除一些属性的其他过滤逻辑。[查看这里，了解更多关于属性发布和过滤的详细信息](https://apereo.github.io/cas/6.0.x/integration/Attribute-Release.html)。
`logoutType` | 定义启动注销协议后怎么处理本服务。可接受的值有`LogoutType.BACK_CHANNEL`、`LogoutType.FRONT_CHANNEL`和`LogoutType.NONE`。[查看这里，了解更多关于logout的信息](https://apereo.github.io/cas/6.0.x/installation/Logout-Single-Signout.html)。
`responseType` | 定义CAS响应服务请求的类型。[查看这里，了解更多](services-response-type.html)。
`usernameAttributeProvider` | 提供者配置，该配置指定什么值作为`username`发送回应用。[查看这里，了解更多关于属性发布和过滤的详细信息](https://apereo.github.io/cas/6.0.x/integration/Attribute-Release.html)。


accessStrategy	The strategy configuration that outlines and access rules for this service. It describes whether the service is allowed, authorized to participate in SSO, or can be granted access from the CAS perspective based on a particular attribute-defined role, aka RBAC. See this guide for more details on attribute release and filters.
publicKey	The public key associated with this service that is used to authorize the request by encrypting certain elements and attributes in the CAS validation protocol response, such as the PGT or the credential. See this guide for more details on attribute release and filters.
logoutUrl	URL endpoint for this service to receive logout requests. See this guide for more details
properties	Extra metadata associated with this service in form of key/value pairs. This is used to inject custom fields into the service definition, to be used later by extension modules to define additional behavior on a per-service basis. See this guide for more info please.
multifactorPolicy	The policy that describes the configuration required for this service authentication, typically for multifactor authentication.
contacts	Specify the collection of contacts associated with service that own the application. See this guide for more info.
