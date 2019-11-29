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



