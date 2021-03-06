---
title: 概述
p: translate/cas-management/index
date: 2019-11-29 08:49:43
type: translate-cas-management
description: CAS web管理系统概述
order: 2
---
[官文地址](https://apereo.github.io/cas-management/5.3.x/installation/Installing-ServicesMgmt-Webapp.html)
## CAS web管理系统

CAS web管理系统不再是CAS server的一部分了，而是一个独立的内置Apache Tomcat容器的Spring Boot web应用。

CAS web管理系统是一个纯粹的管理界面，它可以完全独立于CAS，部署在不同环境上。它可让CAS管理员和应用负责人委托访问，管理和修改他们的应用策略。CAS server本身的操作能力和CAS web管理系统部署状态无关；你可以在任意指定的时间下线维护或彻底删除应用。

<p class="tip"> <b>同步配置</b>  <br>切记：所有应用（CAS server和CAS web管理系统）<b>必须</b>使用相同的服务注册中心配置来管理CAS服务。</p>

注意对于特定后端存储类型的服务注册中心，必须部署web管理系统，因为它充当处理后端存储的CRUD操作界面。如果没有web管理系统，意味着你要找到替代的工具手动管理注册中心的后端存储。

## 安装
### Maven
[这里](https://github.com/apereo/cas-services-management-overlay)提供了一个overlay的模板。
### Gradle
[这里](https://github.com/apereo/cas-services-management--gradle-overlay)提供了一个overlay的模板。
## 配置
相关CAS配置属性列表，[请看这里](configuration.html) 
## 服务注册中心
服务的持久化存储必须和CAS server的持久化存储相同。为CAS server配置的服务注册中心组件（包括模块和配置），需要和web管理系统的配置完全相同。请注意，控制特定服务注册中心的配置属于CAS server，且以`cas`开头。
## 服务管理
CAS中的服务管理器是基于特定注册中心之上的组件，并充当协调器。虽然每个注册中心实例都和存储技术紧密相连，而服务管理器对这个存储技术完全无关，只与注册中心相连，并可能充当缓存层。大多数情况下，这个组件的配置和行为对于CAS发布者是完全透明的。
## Default Vs. Domain
CAS里有两种可用的服务管理器：默认管理器和域名友好的管理器。可通过CAS的配置属性来决定和选择服务管理器。两种形式的服务管理器都可以使用CAS服务注册中心支持的存储层（JSON，YMAL，JDBC，MongoDb，LDAP等等）。它们的区别在于如何加载服务注册中心并将其应用于传进来的请求中。

服务管理器的选择大多是一个偏好的决定。注册中心的大小可能决定选择使用哪个。小于100个服务的较小注册中心可以使用默认管理器，但是成百上千个的服务让人望而生畏，因为顺序是影响行为的重要因素。
### Default
默认管理器以单一有序表的形式加载注册中心。集合的顺序取决于注册服务的`evaluationOrder`字段。当决定哪个服务发起当前请求时，默认管理器从集合头部开始，并尝试将传递的`service`参数与注册服务的serviceId字段匹配。它会遍历整个集合直到找到第一个匹配项，并返回这个服务实例。如果遍历完没有找到匹配项，将会拒绝这个请求并提示用户：`不允许使用CAS来认证您访问的目标应用。`

在组合`serviceId`表达式时需要注意以下几点：

* 必须是一个有效的Java正则表达式。
* 域名分隔符`.`通常需要转义。如果域名中的`.`没有转义(`\.`)可能会得到意想不到的域名匹配。（译者注：`.`在正则表达式里表示匹配任意单字符）
* 域名不能以`.*`结尾。(译者注：`.*`在正则表达式里表示任意字符)

<p class="tip"> 译者提示：<br>
 配置serviceId之前可以先使用Java正则表达式在本地匹配测试，以防匹配有误。</p>

### Domain
Domain管理器将服务加载到多个集合中，并把这些集合存储到set中，根据它们尝试匹配的域建立索引。

管理器首先从请求的`service`参数里提取域。然后从该域索引的列表中查找列表，并像默认管理器那样应用这个较小的列表。有一个在加载服务后创建的特殊的`default`集合。这个集合包含一个可以匹配多个域的`serviceUrl`表达式。如果接收到的请求的域没有在域索引中匹配到，就会使用这个默认集合来处理请求。

像默认管理器一样有一些注意的点：

* 必须是有效的Java正则表达式
* 如果`serviceId`表达式可以匹配多个域，域分隔符`.`必须要转义（译者注：和`Default`管理器一样）
* 如果域中的`.`没有转义，可能会得到意想不到的域匹配
* 如果`serviceId`只能匹配一个域，那么`.`就不需要转义。这条规则可以放宽，因为当查找域集合时，使用的是`equals expression`而不是`matching test`。这意味着只有这个域能与集合中的域匹配。（译者注：精确匹配）

## 搜索
注册中心中的服务可以通过导航面板中的`Search`选项进入搜索。服务的全文检索通过`Apache Lucene`实现。在搜索结果页面上输入关键字点击`Search`执行搜索。数据展示出来后可以点击表格头部进行排序。还可以在这里通过`Lucene QueryParser`语法进行高级搜索。

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
web管理系统可以对服务注册中心的进行版本控制。启用版本控制请确认设置了以下配置：

```properties
mgmt.versionControl.enabled=truel
mgmt.versionContros.ervicesRepo=/etc/cas/services-repo
```
webapp必须要有`servicesRepo`目录的读写权限。版本控制是通过在Git仓库中将注册中心存储为json文件来处理的。webapp启动后，如果指定的仓库不存在，会从配置的注册中心存储中创建仓库，
<p>
译者注：原文有误，6.0.X版本已改成`mgmt.versionControl.enabled`
</p>
### 提交
当webapp未启用版本控制时，对服务做的修改将会立即存入配置的注册中心存储中，并且所有使用相同注册中心的CAS节点都会立即捕捉到这些修改。

启用版本控制后，对服务修改将会在注册中心中标记为`"working change"`。你可以通过导航到`Working changes`视图中查看`working changes`集合。当`working changes`展示后，就会点亮屏幕内容部分右上角的`Commit`选项。

点击`Commit`会打开个对话框展示将要提交的`working changes`。你必须输入提交信息才能完成提交。

这让你可以在单条提交记录中对相关更改进行分组，并在存储库里为它们提供有意义的信息。

### 发布
把更改提交到存储库中后，将会在控制栏中出现`Publish`选项。选择`publish`会打开一个对话框展示关于发布到注册中心存储库中的提交记录，并让你确认这是你想要采取的操作。此时，成功发布后，所有运行中的CAS节点都可以使用这些更改。

### 历史
启用版本控制后，所有对服务的更改都可以在存储库日志中追踪到。你可以在`History`视图中查看所有存储到存储库中的提交记录。选择一个提交记录将会展示服务集合，以及与之对应的更改类型（`ADD`,`MODIFY`,`DELETE`）。

从`repository history`视图中，你可以将注册中心恢复到任意一个提交记录。这首先作为`Working Change`完成，然后你就可以`Commit`和`Publish`。此外，可以使用服务个体菜单中的`“History”`选项从历史中的任何点恢复单个服务。

你还可以在服务上执行`diffs`，比较当前服务于与之前任意版本之间的更改。

## 同步脚本
有个配置项可以将webapp服务的注册中心存储库配置到本地。常用选项是使用`JsonServiceRegistry`将本地注册中心数据持久化到webapp服务。

```properties
cas.serviceRegistry.json.location=file:/etc/cas/services
```

然后，可以在配置中设置在webapp服务运行期间可执行执行的`同步脚本`。

```properties
mgmt.versionControl.syncScript=/etc/cas/sync.sh
```

然后，这个脚本可以使用`rsync`或任何其他方式将服务注册中心同步到CAS节点。

设置`同步脚本`后，会在webapp导航中增加一个`Synchronize`选项，点击这个选项就会执行同步脚本。如果启用了版本控制，这个脚本还会在`发布`更改时由服务器执行。

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

如果使用此策略，则可以进一步通过概述的授权策略来控制访问策略规则。

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

### LDAP
还可以通过以下模式，查询LDAP服务器来直接控制对CAS web管理系统的访问。

#### 分组
授权框架将搜索用户所属的组。检索到的组和角色与CAS web管理系统配置进行比较，以查找匹配项(如`ROLE_ADMIN`)。

#### 属性
授权框架会检查分配给用户的属性，寻找预定义的角色属性与访问配置进行比较（如`ROLE_ADMIN`）。

在WAR overlay模式下需要添加以下依赖来启用本功能的支持：

```xml

<dependency>
  <groupId>org.apereo.cas</groupId>
  <artifactId>cas-management-webapp-support-ldap</artifactId>
  <version>${cas.version}</version>
</dependency>

```
相关CAS属性配置，[请看这里](configuration.html)。

### 自定义
你还可以为CAS web管理系统设计自己的授权生成器：

```java

package org.apereo.cas.support;

@Configuration("myConfiguration")
@EnableConfigurationProperties(CasConfigurationProperties.class)
public class MyConfiguration {

 /**
  * Decide how roles and permissions should be stuffed into the authenticated profile.
  */
  @Bean
  public AuthorizationGenerator authorizationGenerator() {
      ...
  }

 /**
  * Decide the profile should be compared to the required rules for access.
  */
  @Bean
  public Authorizer managementWebappAuthorizer() {
      ...
  }

}

```
## 委托管理
CAS web管理系统中的委托管理特性，只在启用版本控制时可用（看上面）。委托管理通过以下配置启用：

```properties
mgmt.delegated.enabled=true
mgmt.delegated.userReposDir=/etc/cas/user-repos
```
`userReposDir`必须是webapp有读写权限的地址。

### 用户权限
委托用户定义在`user.json`中，就像这样：
```json
{
  "casuser" : {
    "@class" : "org.apereo.cas.mgmt.authz.json.UserAuthorizationDefinition",
    "roles" : [ "ROLE_USER" ],
    "permissions" : [ "DOMAIN1", "DOMAIN2"]
  }
}
```
上面的定义表示`casuser`作为委托用户，它有权限查看/编辑属于'DOMAIN1'和'DOMAIN2'以及子域名的服务。设置权限为`*`，就是授予它们访问注册中心中所有服务的权限，并将用户设置为委托管理员。

### 提交变更
当委托用户对服务作了变更，它们在克隆仓库中作为"Working Change"完成，可以在`management.properties`中配置的`userReposDir`地址里用户登录ID下找到。当工作变更时，内容视图中的控制栏就会点亮"Submit"选项。点击"Submit"会弹出一个对话框，并提示用户输入关于这次变更的"change message"。

用户提交的变更会将working change提交到用户仓库里，并在管理员仓库里新建一个"pull"。

用户从导航进入"Submit"视图，这里将会展示它们所有的"Submit"以及对应的状态（"Pending"、"Accepted"、"Rejected"）。

### Pull Requests
管理员可以从导航栏的`Pull Requests`视图中查看委托用户所有的`pulls`。当有等待审核的`pulls`时，带有请求数量的徽章将会展示在导航栏中。

在`Pull Requests`视图中，管理员可以点击`pull`名字或记录行菜单的`View Change`查看提交的`pull`的所有变更。对于每个变更你都可以以json格式的形式查看变更的`diff`。

变更过的服务也可以在webapp GUI中查看。在此模式中，表单处于`仅查看`模式，无法进行修改。
更改后的字段将以斜体显示，且用不同的颜色表示更改的类型:
* `绿色`：新增
* `蓝色`：修改
* `红色`：删除

在变更的字段下面会有一个`restore`图标。点击这个图标会将这个字段变更为它在注册中心中的当前值，以便进行比较。再次点击就会恢复到变更的值。

查看变更之后，整个`pull`可以作为一个整体`接收`或`拒绝`。当`pull`被接受时，它会立即被提交到仓库，并在控制栏中点亮`Publish`选项。

当拒绝`pull`时，你将有机会在拒绝对话框中列出拒绝更改的原因。
提交用户可以看到这个文本，并可以选择从他们的仓库中还原他们的`pull`，或者做出变更再次提交以供考虑。

### 通知
如果在部署中定义了可用邮件服务器并设置了通知属性，则可以创建和发送电子邮件通知。

定义邮件服务器要添加以下配置：

```properties
spring.mail.host=localhost
spring.mail.port=25
spring.mail.username=
spring.mail.password=
spring.mail.testConnection=false
spring.mail.properties.mail.smtp.auth=false
spring.mail.properties.mail.smtp.starttls.enable=false
```

只有在`management.properties`中定义了`提交pull`、`接受pull`和`拒绝pull`的文本后，才会发送电子邮件。

```properties
mgmt.notifications.submit.text=Your submitted changes have been forwarded to a CAS Administrator for approval. You will be notified by email once again when your request has been processed.\n\nYou can review the status of this request through the CAS Management Appication by navigating to the "Submit Requests" screen.\n\n If you have further questions or require assistance, please contact help@domain.
mgmt.notifications.submit.from=casmanager@domain
mgmt.notifications.submit.subject=Request {0} has been submitted for approval
mgmt.notifications.submit.cc=admin-list@domain
mgmt.notifications.submit.bcc=

mgmt.notifications.reject.text=Your request {0} to the CAS Service Registry has been rejected for the following reason:\n\n{1}\n\n Please login into the CAS Management Application and "Revert" your submit.  You then have the option to make any recommended changes and submit your request again.\n\nIf you need further help with this request, please contact help@domain.
mgmt.notifications.reject.from=casmanager@domain
mgmt.notifications.reject.subject=Request {0} has been rejected
mgmt.notifications.reject.cc=admin-list@domain

mgmt.notifications.accept.text=Your request {0} has been accepted and added to the CAS Service Registry, and should be effective immediately in CAS.\n\nIf you do not see the behavior you expected, please submit a request to ithelp@ucdavis.edu.  Do not submit your request a second time.
mgmt.notifications.accept.from=casmanager@domain
mgmt.notifications.accept.subject=Request {0} has been accepted
mgmt.notifications.accept.cc=admin-list@domain
```

每条消息的`subject`要有`{0}`参数，用于展示提交的ID。

`accept`消息的`text`要有`{0}`参数，用于展示提交的ID。

`reject`消息的`text`要有`{0}`参数来展示提交的ID，以及`{1}`参数来展示管理员提供的拒绝原因。

为了将邮件发送给提交用户，你要确保在登录webapp时CAS发布了`email`属性。
