---
title: 安装
p: translate/cas-management/index
date: 2019-11-29 08:49:43
type: translate-cas-management
description: CAS后台管理系统安装
---
[官文地址](https://apereo.github.io/cas-management/5.3.x/installation/Installing-ServicesMgmt-Webapp.html)
## CAS web管理系统

CAS web管理系统不再是CAS server的一部分了，而是一个独立的内置Apache Tomcat容器的Spring Boot web应用。

CAS web管理系统是一个可以完全独立于CAS，部署在不同环境上的管理界面。它可让CAS管理员和应用负责人委托访问，管理和修改应用的策略。CAS server本身的操作能力和CAS web管理系统部署状态无关；你可以在任意指定的时间下线维护应用或彻底删除。

> 同步配置
> 切记：所有应用（CAS server和CAS web管理系统）必须使用相同的服务注册中心配置来管理CAS服务。

注意对于某些类型的服务注册中心的后端存储，需要部署web管理系统，因为它可作为处理后端存储的CRUD操作界面。如果没有web管理系统，你要找到替代的工具手动管理注册中心的后端存储。

## 安装
### Maven
[这里](https://github.com/apereo/cas-services-management-overlay)提供了一个overlay的模板。
### Gradle
[这里](https://github.com/apereo/cas-services-management--gradle-overlay)提供了一个overlay的模板。
## 配置
CAS配置属性列表，[请看这里](configuration.html) 
## 服务注册中心
服务的持久化存储必须和CAS server的持久化存储相同。CAS server配置的相同的服务注册中心组件，包括模块和配置，需要和web管理系统的配置完全相同。请注意，控制特定服务注册中心的配置属于CAS server，且以`cas`开头。
## 服务管理
CAS中的服务管理器是基于特定注册中心之上的组件，并充当协调器。虽然每个注册中心实例都和存储技术紧密相连，而服务管理器对这个存储技术完全无关，只与注册中心相连，并可能充当缓存层。大多数情况下，这个组件的配置和行为对于CAS发布者是完全透明的。
## Default Vs. Domain
CAS里有两种可用的服务管理器：默认选择和另一个域名友好的。可通过CAS的配置属性来决定和控制这个选择。两种形式的服务管理器都可以使用CAS服务注册中心支持的存储层（JSON，YMAL，JDBC，MongoDb，LDAP等等）。它们的区别在于如何加载和应用服务注册中心到进来的请求中。
服务管理器的选择大多是一个偏好的决定。注册中心的大小可能决定选择使用哪个。小于100个服务的较小注册中心可以使用默认管理器，而考虑到顺序是可能影响行为的重要因素，成百上千个的服务让人望而生畏。
### Default
默认管理器以单一有序表的形式加载注册表。集合的顺序取决于注册服务的`evaluationOrder`字段。当决定哪个服务发起当前请求时，默认管理器从集合头部开始，并尝试将传递的`service`参数与注册服务的serviceId字段匹配。它会遍历整个集合直到找到第一个匹配性，并返回这个服务实例。如果遍历完没有找到匹配项，将会拒绝这个请求并提示用户：`不允许使用CAS来认证您访问的目标应用。`
在组合serviceId表达式时需要注意以下几点：
* 必须是一个有效的Java正则表达式。
* 域名分隔符`.`通常需要转义。如果域名中的`.`没有转义(`\.`)可能会得到意想不到的域名匹配。（译者注：`.`在正则表达式里表示匹配任意单字符）
* 域名不能以`.*`结尾。(译者注：`.*`在正则表达式里表示任意字符)
> 译者提示：
> 配置serviceId之前可以先使用Java正则表达式在本地匹配测试，以防匹配有误。

### Domain
域管理器将服务加载到多个集合中，并把这些集合存储到set中，根据它们尝试匹配的域建立索引。
管理器首先从请求的`service`参数里提取域。然后从该域索引的一组集合中查找，并像默认管理器那样应用这个小集合。有一个特殊的在加载服务后创建的`default`集合。这个集合包含一个可以匹配多个域的`serviceUrl`表达式。如果接收到的请求的域没有在域索引中匹配到，就会使用这个默认集合来处理请求。
像默认管理器一样有一些注意的点：
* 必须是有效的Java正则表达式
* 如果`serviceId`表达式可以匹配多个域，域分隔符`.`必须要转义（译者注：和`Default`管理器一样）
* 如果域中的`.`没有转义，可能会得到意想不到的域匹配
* 如果`serviceId`只能匹配一个域，那么`.`就不需要转义。这条规则可以放宽，因为当查找域集合时，使用的是`equals expression`而不是`matching test`。这意味着只有这个域能与集合中的域匹配。（译者注：精确匹配）

## 搜索
注册表中的服务可以通过导航面板中的`Search`选项进行搜索。服务的全文检索通过`Apache Lucene`支持。在搜索结果页面上输入关键字点击`Search`执行搜索。数据展示出来后可以点击表格头部进行排序。高级搜索可以在这里通过`Lucene QueryParser`语法搜索。
通常情况下，如果搜索时限定服务的特定字段，搜索将会执行的更快、结果更精确。
json格式的服务中使用的`keys`可以用作查询中的字段名：
```properties
name: Apereo
```
嵌套字段的查询使用`.`：
```properties
multifactorPolicy.bypassEnabled: true
```
还可以使用组合查询：
```properties
name: Apereo AND multifactorPolicy.bypassEnabled: true
```
