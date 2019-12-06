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

<p class="tip"> <b>同步配置</b>  <br>切记：所有应用（CAS server和CAS web管理系统）必须使用相同的服务注册表配置来管理CAS服务。</p>

注意对于某些类型的服务注册表的后端存储，需要部署web管理系统，因为它可作为处理后端存储的CRUD操作界面。如果没有web管理系统，你要找到替代的工具手动管理注册表的后端存储。

## 安装
### Maven
[这里](https://github.com/apereo/cas-services-management-overlay)提供了一个overlay的模板。
### Gradle
[这里](https://github.com/apereo/cas-services-management--gradle-overlay)提供了一个overlay的模板。
## 配置
CAS配置属性列表，[请看这里](configuration.html) 
## 服务注册表
服务的持久化存储必须和CAS server的持久化存储相同。CAS server配置的相同的服务注册表组件，包括模块和配置，需要和web管理系统的配置完全相同。请注意，控制特定服务注册表的配置属于CAS server，且以`cas`开头。
## 服务管理
CAS中的服务管理器是基于特定注册表之上的组件，并充当协调器。虽然每个注册表实例都和存储技术紧密相连，而服务管理器对这个存储技术完全无关，只与注册表相连，并可能充当缓存层。大多数情况下，这个组件的配置和行为对于CAS发布者是完全透明的。
## Default Vs. Domain
CAS里有两种可用的服务管理器：默认选择和另一个域名友好的。可通过CAS的配置属性来决定和控制这个选择。两种形式的服务管理器都可以使用CAS服务注册表支持的存储层（JSON，YMAL，JDBC，MongoDb，LDAP等等）。它们的区别在于如何加载和应用服务注册表到进来的请求中。

服务管理器的选择大多是一个偏好的决定。注册表的大小可能决定选择使用哪个。小于100个服务的较小注册表可以使用默认管理器，而考虑到顺序是可能影响行为的重要因素，成百上千个的服务让人望而生畏。
### Default
默认管理器以单一有序表的形式加载注册表。集合的顺序取决于注册服务的`evaluationOrder`字段。当决定哪个服务发起当前请求时，默认管理器从集合头部开始，并尝试将传递的`service`参数与注册服务的serviceId字段匹配。它会遍历整个集合直到找到第一个匹配性，并返回这个服务实例。如果遍历完没有找到匹配项，将会拒绝这个请求并提示用户：`不允许使用CAS来认证您访问的目标应用。`

在组合serviceId表达式时需要注意以下几点：

* 必须是一个有效的Java正则表达式。
* 域名分隔符`.`通常需要转义。如果域名中的`.`没有转义(`\.`)可能会得到意想不到的域名匹配。（译者注：`.`在正则表达式里表示匹配任意单字符）
* 域名不能以`.*`结尾。(译者注：`.*`在正则表达式里表示任意字符)

<p class="tip"> 译者提示：<br>
 配置serviceId之前可以先使用Java正则表达式在本地匹配测试，以防匹配有误。</p>

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
## 版本控制
web管理系统可以对服务注册表的进行版本控制。启用版本控制请确认设置了以下配置：

```properties
mgmt.enableVersionControl=true
mgmt.servicesRepo=/etc/cas/services-repo
```
webapp必须要有`servicesRepo`目录的读写权限。版本控制是通过在Git仓库中将注册表存储为json文件来处理的。webapp启动后，如果指定的仓库不存在，会从配置的注册表存储中创建仓库，
<p>
译者注：6.0.X版本已改成`mgmt.versionControl.enabled`
</p>
### 提交
当webapp未启用版本控制时，对服务做的修改将会立即存入配置的注册表存储中，并且所有使用相同注册表的CAS节点都会立即捕捉到这些修改。

启用版本控制后，对服务修改将会在注册表中标记为`"working change"`。你可以通过导航到`Working changes`视图中查看`working changes`集合。当`working changes`展示后，就会点亮屏幕内容部分右上角的`Commit`选项。

点击`Commit`会打开个对话框展示将要提交的`working changes`。你必须输入提交信息才能完成提交。

这使你可以在单条提交记录中对相关更改进行分组，并在存储库里为它们提供有意义的信息。

### 发布
把更改提交到存储库中后，将会在控制栏中出现`Publish`选项。选择`publish`会打开一个对话框展示关于发布到注册表存储库中的提交记录，并让你确认这是你想要采取的操作。此时，成功发布后，所有运行中的CAS节点都可以使用这些更改。

### 历史
启用版本控制后，所有对服务的更改都可以在存储库日志中追踪到。你可以在`History`视图中查看所有存储到存储库中的提交记录。选择一个提交记录将会展示服务集合，以及与之对应的更改类型（`ADD`,`MODIFY`,`DELETE`）。

从`repository history`视图中，你可以将注册表恢复到任意一个提交记录。这首先作为`Working Change`完成，然后你就可以`Commit`和`Publish`。此外，可以使用服务个体菜单中的`“History”`选项从历史中的任何点恢复单个服务。

你还可以在服务上执行`diffs`，比较当前服务于与之前任意版本之间的更改。

## 同步脚本
有个配置项可以将webapp服务的注册表存储库配置到本地。常用选项是使用`JsonServiceRegistry`将本地注册表数据持久化到webapp服务。

```properties
cas.serviceRegistry.json.location=file:/etc/cas/services
```

然后，可以在配置中设置在webapp服务运行期间可执行执行的`同步脚本`。

```properties
mgmt.syncScript=/etc/cas/sync.sh
```

然后，这个脚本可以使用`rsync`或任何其他方式将服务注册表同步到CAS节点。

设置`同步脚本`后，会在webapp导航中添加一个`Synchronize`选项，点击这个选项就会执行同步脚本。如果启用了版本控制，这个脚本还会在`发布`更改时由服务器执行。

同步脚本需要返回`0`表示执行成功，以及任意大于`0`的数字表示发生异常。

## 表单数据
在启动时，webapp会尝试连接配置的CAS服务的`status/discovery`端点。如果成功，从这个端点获取的数据将会用于填充表单中的以下字段选项：

* 注册服务类型
* MFA提供者类型
* 委托的身份验证客户端类型
* 可用的发布属性

这将把选项列表缩小到您当前配置的CAS部署所支持的范围。

如果`status/discovery`端点没有启用或者webapp不能成功连接到这个端点，那么不管当前的部署是否支持，表单数据都会在CAS中的默认展示所有可用选项。

### 可用属性
如果你不能使用`status/discovery`端点来填充发布中的可用属性列表，你可以在配置中定义StubAttributeRepository来手动设置。

```properties
cas.authn.attributeRepository.stub.attributes.uid=uid
cas.authn.attributeRepository.stub.attributes.givenName=givenName
cas.authn.attributeRepository.stub.attributes.eppn=eppn
```

## 认证
可以配置管理webapp的以下访问策略：

### CAS服务
CAS web管理系统的身份认证可以配置为CAS服务。要激活此模式，只需要在配置里指定CAS服务地址即可。若要禁用此模式，只需要将CAS服务地址设置为空即可。

如果使用此策略，则可以进一步通过授权概述策略来控制访问策略规则。

相关CAS属性配置，[请看这里](configuration.html)。

### IP地址
CAS web管理系统可配置为匿名访问，匿名访问时请求的IP要和预定义的IP正则表达式匹配。若要禁用此策略，将CAS服务设置为空即可。如果使用此策略，因为解析的身份是简单的匿名用户，所以访问策略和授权规则都不再应用。

<p class="tip"> 注意：<br>
请记住，此授权机制只对相对静态IP地址的内网客户端启用。
</p>

相关CAS属性配置，[请看这里](configuration.html)。

### 匿名访问
如果没有其他的鉴权配置，CAS web管理系统会默认配置为允许匿名访问。此模式主要用于开发和测试，而在解释此选项的警告的日志中会出现其他警告。

<p class="tip"> 注意：<br>
一定要指定身份验证策略，因为如果不这样做，就会使CAS web管理系统的访问对外开放。
</p>

## 授权
了解如何控制CAS web管理系统的访问。下面的选项介绍了如何生成已认证用户的授权规则，并将此规则应用到web管理系统。角色、权限等一旦生成，就会填完全充到认证用户，并跟CAS web管理系统中定义的访问规则进行匹配。实质上，执行以下步骤：

* 加载进入web管理系统所必需的认证用户的角色和权限。
* 认证指定的用户并建立一个profile。
* 使用包含角色、权限等的授权规则填充profile。
* 将profile与所要求的规则和权限进行比较。

### 静态用户集合
#### Properties
默认情况下，访问权限由一个静态用户列表限制，这些用户的凭据可以在单个属性文件中指定，该属性文件在运行时受到监视和监视，以进行更改并自动重新加载。文件的格式模仿了`Spring Security`，其中存放了访问web管理系统的授权用户列表，即:

```properties
# casuser=notused,ROLE_ADMIN
```

文件的格式如下：

* `casuser`: 从CAS接收到的通过身份验证的用户ID
* `notused`: 这是CAS不使用的密码字段。你可以在这个地方放置任何你想设置的值。
* `ROLE_ADMIN`: 分配给授权管理员的角色属性，然后根据CAS配置对其进行交叉检查。
* `ROLE_USER`: 分配给授权用户的角色属性，然后根据CAS配置对其进行交叉检查。

#### JSON & YAML
基于文件的授权规则还可以定义在`.json`和`.yml`中，来映射用户名的角色和权限。JSON示例如下：

```json
{
  "casuser" : {
    "@class" : "org.apereo.cas.mgmt.authz.json.UserAuthorizationDefinition",
    "roles" : [ "ROLE_ADMIN" ],
    "permissions" : [ "CAN_DO_XYZ" ]
  }
}
```
### 属性
或者，授权生成器检查CAS验证响应和登录主体的属性，如果属性名与配置中定义的`adminRoles`的值匹配，则允许访问。




