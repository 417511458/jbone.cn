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
能够接受多个值的配置通常用索引记录，如`mgmt.some.setting[0]=value`。索引`[0]`表示由采用者递增，以允许使用不同的多个配置块：

```properties
# mgmt.some.setting[0]=value1
# mgmt.some.setting[1]=value2
```

## 属性
```properties
# mgmt.adminRoles[0]=ROLE_ADMIN
# mgmt.userRoles[0]=ROLE_USER

# mgmt.serverName=https://localhost:8443
# mgmt.defaultLocale=en

# mgmt.authzIpRegex=
# mgmt.authzAttributes[0]=
# mgmt.userPropertiesFile=classpath:user-details.properties

# mgmt.versionControl.servicesRepo=/etc/cas/services-repo
# mgmt.versionControl.enabled=false
# mgmt.versionControl.syncScript=


# mgmt.delegated.enabled=false
# mgmt.delegated.userReposDir=/etc/cas/user-repos
# mgmt.enableDiscoveryEndpointCall=true
```

> 译者注：原文中的以上配置有些错误，已在本文改正。

### 通知
**配置键**
* `mgmt.notifications.submit`
* `mgmt.notifications.accept`
* `mgmt.notifications.reject`

以下属性适用于上面**配置键**的通知配置：

```properties
# ${configurationKey}.attributeName=mail
# ${configurationKey}.text=
# ${configurationKey}.from=
# ${configurationKey}.subject=
# ${configurationKey}.cc=
# ${configurationKey}.bcc=
```

### LDAP
```properties
# mgmt.ldap.ldapUrl = ldap://localhost:389
# mgmt.ldap.bindDn =
# mgmt.ldap.bindCredential =
# mgmt.ldap.useSsl = true

# mgmt.ldap.trustCertificates =
# mgmt.ldap.keystore = 
# mgmt.ldap.keystorePassword
# mgmt.ldap.keystoreType = 

# mgmt.ldap.minPoolSize = 3
# mgmt.ldap.maxPoolSize = 10
# mgmt.ldap.poolPassivator = BIND
# mgmt.ldap.validateOnCheckout = true
# mgmt.ldap.validatePeriodically = true 
# mgmt.ldap.validateTimeout = PT5S
# mgmt.ldap.validatePeriod = PT5M 

# mgmt.ldap.failFast = true
# mgmt.ldap.idleTime = PT10M
# mgmt.ldap.prunePeriod = PT2H
# mgmt.ldap.blockWaitTime = PT3S
# mgmt.ldap.connectionStrategy =
# mgmt.ldap.useStartTls = 
# mgmt.ldap.connectTimeout = PT5S
# mgmt.ldap.responseTimeout = PT5S
# mgmt.ldap.providerClass = 
# mgmt.ldap.allowMultipleDns = false
# mgmt.ldap.saslRealm = 
# mgmt.ldap.saslMechanism = 
# mgmt.ldap.saslAuthorizationId = 
# mgmt.ldap.saslSecurityStrength = 
# mgmt.ldap.saslMutualAuth = 
# mgmt.ldap.saslQualityOfProtection = 
```

#### LDAP授权
```properties
# mgmt.ldap.ldapAuthz.roleAttribute = uugid
# mgmt.ldap.ldapAuthz.rolePrefix = ROLE_
# mgmt.ldap.ldapAuthz.allowMultipleResults
# mgmt.ldap.ldapAuthz.groupAttribute
# mgmt.ldap.ldapAuthz.groupPrefix =
# mgmt.ldap.ldapAuthz.groupFilter
# mgmt.ldap.ldapAuthz.groupBaseDn
# mgmt.ldap.ldapAuthz.baseDn
# mgmt.ldap.ldapAuthz.searchFilter
```


