---
title: 配置
description: CAS web管理系统配置
keywords: CAS management,CAS管理
p: translate/cas-management/configuration
date: 2019-11-29 08:53:51
type: translate-cas-management
order: 3
---

CAS web管理系统的配置主要由`/etc/cas/config/management.(yml|properties)`文件控制。请注意，CAS支持的所有配置策略也应同样适用于这里，以定义、传递和解析配置的方式。当然，主要的区别是文件的名字。

## 命名约定
* 由CAS平台直接控制的配置和属性总是以`mgmt`前缀开头。所有通过其他底层框架提供给CAS的其他的配置，它们可能有自己的模式和语法，注意这种区别。
* 无法识别的属性会被CAS和/或CAS所依赖的框架拒绝。这意味着如果你以某种方式拼错属性或者不遵循点符号语法等，你的配置将会被CAS完全拒绝，它所控制的功能可能永远不会以你想要的方式激活。

## 索引设置




