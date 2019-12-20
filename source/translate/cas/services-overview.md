---
title: 概述
date: 2019-11-29 08:49:43
type: translate-cas
description: CAS服务管理概述
keywords: CAS服务管理，官方文档，翻译
order: 1
---

CAS服务管理设施可让CAS服务管理员声明和配置服务（CAS客户端）使用CAS的方式。服务管理设施的核心组件是服务注册中心，它存储一个或多个注册服务，这些注册服务包含驱动CAS行为的元数据：

* [授权服务](services-access-strategy.html) - 控制哪些服务可以参与CAS SSO会话。
* 强制身份认证 - 为强制身份认证提供管理控制。
* 属性发布 - 为服务的授权和个性化设置提供用户详情。
* [代理控制](services-proxy-policy.html) - 通过授予/拒绝代理身份认证功能，进一步控制授权服务。
* 主题控制 - 为特定服务定义备用CAS主题。

相关CAS属性配置，[请看这里](https://apereo.github.io/cas/6.0.x/configuration/Configuration-Properties.html#service-registry)。

## 服务管理web应用
服务管理webapp是一个独立的web应用程序，它提供了管理服务注册中心数据的GUI，可以独立于CAS部署。web管理系统必须和CAS server共享相同的注册中心配置，以便所有系统都可以加载相同的服务数据。想了解更多关于管理webapp的信息，[请看这里](../cas-management/overview.html)。

## 注册服务
注册服务提供了以下元数据：

字段 | 描述
--- | --- 
`id` | 必填的唯一标识符。必须是一个有效的数字。
`name` | 必填的名字（少于等于255个字符）。
`description` | 可选的，服务的自由文本描述。（少于等于255个字符）。
`informationUrl` | 可选的，指向服务信息指南自由文本链接。
`privacyUrl` | 可选的，指向服务隐私策略自由文本链接。
`logo` | 可选的，服务的logo图片路径。该图片会与`description`和`name`一起展示在登录页面。图片路径可以是CAS应用的图片目录的相对路径，也可以是完整的URL。
`serviceId` | 必填的，描述逻辑服务的正则表达式。逻辑服务定义了一个或多个服务所在的URL。url的匹配定义必须要格外小心，因为它可能造成安全漏洞。
`theme` | 可选的，当服务请求票据时，用于自定义CAS UI的主题名。[查看这里，了解更多](https://apereo.github.io/cas/6.0.x/ux/User-Interface-Customization.html)
`proxyPolicy` | 确定服务是否可以代理身份认证。[查看这里，了解更多](services-proxy-policy.html)。
`evaluationOrder` | 确定已注册服务的相对估值顺序。这个标记在两个服务的URL表达式覆盖相同服务的场景中尤其重要；估值顺序决定首先评估哪个注册信息，并充当内部排序因子。
`requiredHandlers` | 一组身份认证处理器名字，这些身份认证处理器必须成功的认证凭证后才能访问服务。如果定义了身份认证处理器，就只选择指定处理器来认证这个注册服务的请求。[查看这里，了解更多](services-required-auth.html)。
`attributeReleasePolicy` | 描述可发布到应用的属性集的策略。以及其他需要清除一些属性的其他过滤逻辑。[查看这里，了解更多关于属性发布和过滤的信息](https://apereo.github.io/cas/6.0.x/integration/Attribute-Release.html)。
`logoutType` | 定义启动注销协议后怎么处理本服务。可接受的值有`LogoutType.BACK_CHANNEL`、`LogoutType.FRONT_CHANNEL`和`LogoutType.NONE`。[查看这里，了解更多关于logout的信息](https://apereo.github.io/cas/6.0.x/installation/Logout-Single-Signout.html)。
`responseType` | 定义CAS响应服务请求的类型。[查看这里，了解更多](services-response-type.html)。
`usernameAttributeProvider` | 提供者配置，该配置指定什么值作为`username`发送回应用。[查看这里，了解更多关于属性发布和过滤的信息](https://apereo.github.io/cas/6.0.x/integration/Attribute-Release.html)。
`accessStrategy` | 概述和访问本服务的规则策略配置。该配置描述了服务是否可以在SSO中授权，或者是否可以根据特定的属性定义的角色(即RBAC)从CAS授予访问权。[查看这里，了解更多关于属性发布和过滤的信息](https://apereo.github.io/cas/6.0.x/integration/Attribute-Release.html)。
`publicKey` | 服务关联的公钥，该公钥用于通过加密CAS验证协议响应中的某些元素和属性（如PGT或凭证）来对请求授权。[查看这里，了解更多关于属性发布和过滤的信息](https://apereo.github.io/cas/6.0.x/integration/Attribute-Release.html)。
`logoutUrl` | 服务接收注销请求的URL端点。[查看这里，了解更多](https://apereo.github.io/cas/6.0.x/installation/Logout-Single-Signout.html)。
`properties` | 服务关联的键值对形式的扩展元数据。该配置用于向服务定义注入自定义字段，然后扩展模块将使用这些字段在每个服务的基础上定义其他行为。[查看这里，了解更多](services-custom-properties.html)。
`multifactorPolicy` | 描述本服务身份认证所需配置的策略，通常用于多因素身份认证。
`contacts` | 指定服务相关应用负责人的联系人集合。[查看这里，了解更多](services-contacts-and-owners.html)。

> 服务类型
> 注意，尽管上面的属性适用于所有通用服务定义，但是根据使用的协议和客户端应用的不同，还可能激活和需要其他服务类型。请根据您关心的功能，查看专用指南（如OAuth、SAML等）。

### 服务访问策略
[查看这里，了解更多](services-access-strategy.html)。

### 代理身份认证策略
[查看这里，了解更多](services-proxy-policy.html)。

### 身份认证要求
[查看这里，了解更多](services-required-auth.html)。

### 标签 & 属性
[查看这里，了解更多](services-custom-properties.html)。

### 联系人和所有者
[查看这里，了解更多](services-contacts-and-owners.html)。

### 过期策略
[查看这里，了解更多](services-expiration-policy.html)。

## 存储
CAS中提供了以下存储服务的方式。

存储 | 描述 | 用法
---|---|---
`Memory` | [使用指南](services-storage-inmemory.html) | 将服务定义XML存储在内存中。变更时需要重新打包CAS并重新启动服务。
`JSON` | [使用指南](services-storage-json.html) | 将服务定义存储在JSON文件中。高可用部署时需要复制服务定义。
`YAML` | [使用指南](services-storage-yaml.html) | 和`JSON`一样。
`MongoDb` | [使用指南](services-storage-mongodb.html) | 将服务定义存储在MongoDb中。高可用部署候选方案。
`Redis` | [使用指南](services-storage-redis.html) | 将服务定义存储在Redis中。高可用部署候选方案。
`LDAP` | [使用指南](services-storage-ldap.html) | 将服务定义存储在目录服务器中。高可用部署候选方案。
`JPA` | [使用指南](services-storage-jpa.html) | 将服务定义存储在关系型数据库中（Oracle、MySQL等)。高可用部署候选方案。
`Couchbase` | [使用指南](services-storage-couchbase.html) | 将服务定义存储在Couchbase中。高可用部署候选方案。
`DynamoDb` | [使用指南](services-storage-dynamodb.html) | 将服务定义存储在DynamoDb中。高可用部署候选方案。
`CosmosDb` | [使用指南](services-storage-cosmosdb.html) | 将服务定义存储在CosmosDb中。高可用部署候选方案。
`REST` | [使用指南](services-storage-rest.html) | 以REST API的方式设计自己的服务注册中心实现。高可用部署候选方案。
`Custom` | [使用指南](services-storage-custom.html) | 使用CAS API作为扩展来设计自己的服务注册中心实现。高可用部署候选方案。

### 怎么选择？
上面有各种各样的服务注册中心，甄选准则如下：

* 选择一种你最熟悉的技术，并有技能和耐心去排除故障、调优和衡量，以获得胜利。
* 选择一种不会强制你的CAS配置绑定到集群中的单个服务/节点的技术，因为这样会带来自动扩展问题和手动工作。
* 选择一种能在您的网络和防火墙配置中运行良好的技术，并且可以基于您的网络拓扑具有足够的性能和可靠性。
* 选择一种在预期负载下显示出鼓舞人心的结果的技术，并进行性能和压力测试。
* 选择一种尽可能不依赖于外部流程、系统和手动操作，自力更生且自成体系的技术。
