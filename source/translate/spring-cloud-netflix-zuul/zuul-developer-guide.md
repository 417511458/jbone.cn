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

Zuul是以Servlet方式实现的。对于一般情况，Zuul被嵌入到`Spring Dispatch`机制中。这使得Spring MVC可以控制路由。在这种情况下，Zuul缓冲请求。如果在经过Zuul时不需要缓冲请求（例如，上传大文件），在Spring Dispatcher之外也安装了Zuul Servlet。默认情况下，这个servlet的地址是`/zuul`。可以通过`zuul.servlet-path`属性拉修改这个地址。

> 译者注：
> Zuul Servlet是一个公共实现，默认情况下，Spring Cloud会初始化两个入口，`ZuulController`和`ZuulServlet`。对于一般请求都是通过`ZuulController`；对于特殊需求（如上传大文件）可通过`ZuulServlet`。

## Zuul RequestContext

Zuul使用`RequestContext`在过滤器之间传递信息。其数据保存在每个特定请求的`ThreadLocal`里。有关路由的请求地址、错误、以及真正的`HttpServletRequest`和`HttpServletResponse`的信息都存储在这里。`RequestContext`继承自`ConcurrentHashMap`，所以可以将任意信息存储在上下文中。`FilterConstants`中包含了由Spring Cloud Netflix安装的过滤器使用的`key`。

##  `@EnableZuulProxy` vs. `@EnableZuulServer`

根据启用Zuul所使用的注解方式的不同，Spring Cloud Netflix 安装了不同的若干过滤器。`@EnableZuulProxy`是`@EnableZuulServer`的超集。换句话说，`@EnableZuulProxy`包含由`@EnableZuulServer`安装的所有过滤器。`Proxy`中附加的过滤器启用了路由功能。如果您想用`空白`的Zuul，请使用`@EnableZuulServer`。（译者注：这里的`空白`指的是不含路由功能）

## `@EnableZuulServer`过滤器

`@EnableZuulServer`创建了一个`SimpleRouteLocator`,它从Spring Boot配置文件中加载路由定义。

安装了以下过滤器（就像普通的Spring Beans）：

* Pre过滤器
    * `ServletDetectionFilter`：检测请求是否通过Spring Dispatcher。使用`FilterConstants.IS_DISPATCHER_SERVLET_REQUEST_KEY`来设置一个boolean值。
    * `FormBodyWrapperFilter`：解析表单数据并为下游请求重新编码。
    * `DebugFilter`：如果设置了`debug`请求参数，那么会将`RequestContext.setDebugRouting()` 和 `RequestContext.setDebugRequest()`设置为`true`。
    * `SendForwardFilter`：使用Servlet`RequestDispatcher`转发请求。转发地址存储在`RequestContext`的`FilterConstants.FORWARD_TO_KEY`属性里。这对于转发到当前应用中的端点非常有用。
* Post过滤器
    * `SendResponseFilter`：将代理请求的响应写到当前响应里。
* Error过滤器
    * `SendErrorFilter`：如果`RequestContext.getThrowable()`非空，则转发到`/error`（默认值）。您可以通过`error.path`属性修改默认转发路径（`/error`）。

## `@EnableZuulProxy`过滤器

创建了一个`DiscoveryClientRouteLocator`，它从`DiscoveryClient`（如`Eureka`）以及配置文件加载路由定义。从`DiscoveryClient`为每个`serviceId`创建一个路由。随着新服务的添加，路由将会被刷新。

除了安装了前面描述的过滤器之外（译者注：`@EnableZuulServer`过滤器），还安装了以下过滤器（就像普通的Spring Beans）：

* Pre过滤器：
    * `PreDecorationFilter`：根据提供的`RouteLocator`来确定路由的位置和方式。它还为下游请求设置了各种与代理相关的头信息。
* Route过滤器：
    * `RibbonRoutingFilter`：使用`Ribbon`、`Hystrix`、以及可插拔的`HTTP客户端`发送请求。通过`RequestContext`的`FilterConstants.SERVICE_ID_KEY`属性来获取ServiceId。该过滤器可使用不同的HTTP客户端：
        * Apache `HttpClient`：默认客户端。
        * Squareup `OkHttpClient` v3：通过添加`com.squareup.okhttp3:okhttp`包，并设置`ribbon.okhttp.enabled=true`来启用。
        * Netflix Ribbon HTTP client: 通过设置`ribbon.restclient.enabled=true`来启用。该客户端有一些限制，其中包括不支持`PATCH`方法，但它内置了重试功能。
    * `SimpleHostRoutingFilter`：通过Apache HttpClient向预先确定的URLs发送请求。这里的URLs通过`RequestContext.getRouteHost()`获取。

## 自定义Zuul过滤器实例

以下大部分`如何写`的实例都在 [Sample Zuul Filters](https://github.com/spring-cloud-samples/sample-zuul-filters) 项目中。该仓库中还有一些操纵请求体和响应体的实例。

本章节包含以下示例：

* 如何写Pre Filter
* 如何写Route Filter
* 如何写Post Filter

### 如何写Pre Filter

Pre过滤器在`RequestContext`中设置数据，以便下游过滤器使用。Pre过滤器主要的使用场景是为`route`过滤器设置所需的信息。以下示例展示了一个Zuul pre过滤器：

```java
public class QueryParamPreFilter extends ZuulFilter {
	@Override
	public int filterOrder() {
		return PRE_DECORATION_FILTER_ORDER - 1; // run before PreDecoration
	}

	@Override
	public String filterType() {
		return PRE_TYPE;
	}

	@Override
	public boolean shouldFilter() {
		RequestContext ctx = RequestContext.getCurrentContext();
		return !ctx.containsKey(FORWARD_TO_KEY) // a filter has already forwarded
				&& !ctx.containsKey(SERVICE_ID_KEY); // a filter has already determined serviceId
	}
    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
		HttpServletRequest request = ctx.getRequest();
		if (request.getParameter("sample") != null) {
		    // put the serviceId in `RequestContext`
    		ctx.put(SERVICE_ID_KEY, request.getParameter("foo"));
    	}
        return null;
    }
}

```

上面的过滤器从`sample`请求参数填充`SERVICE_ID_KEY`。在实践中，您不应该进行这样的直接映射。相反，应该通过`sample`的值来查找service ID。

由于填充了`SERVICE_ID_KEY`，那么就不执行`PreDecorationFilter`了，而会执行`RibbonRoutingFilter`。

> 如果您想路由到完整的URL，请使用`ctx.setRouteHost(url)`。

要修改路由过滤器转发的路径，请设置`REQUEST_URI_KEY`。

### 如何写Route Filter

Route过滤器运行在Pre过滤器之后，并向其它服务发送请求。这里的大部分工作是将请求和响应数据与客户端所需的模型相互转换。下面的例子展示了一个Route过滤器：

```java
public class OkHttpRoutingFilter extends ZuulFilter {
	@Autowired
	private ProxyRequestHelper helper;

	@Override
	public String filterType() {
		return ROUTE_TYPE;
	}

	@Override
	public int filterOrder() {
		return SIMPLE_HOST_ROUTING_FILTER_ORDER - 1;
	}

	@Override
	public boolean shouldFilter() {
		return RequestContext.getCurrentContext().getRouteHost() != null
				&& RequestContext.getCurrentContext().sendZuulResponse();
	}

    @Override
    public Object run() {
		OkHttpClient httpClient = new OkHttpClient.Builder()
				// customize
				.build();

		RequestContext context = RequestContext.getCurrentContext();
		HttpServletRequest request = context.getRequest();

		String method = request.getMethod();

		String uri = this.helper.buildZuulRequestURI(request);

		Headers.Builder headers = new Headers.Builder();
		Enumeration<String> headerNames = request.getHeaderNames();
		while (headerNames.hasMoreElements()) {
			String name = headerNames.nextElement();
			Enumeration<String> values = request.getHeaders(name);

			while (values.hasMoreElements()) {
				String value = values.nextElement();
				headers.add(name, value);
			}
		}

		InputStream inputStream = request.getInputStream();

		RequestBody requestBody = null;
		if (inputStream != null && HttpMethod.permitsRequestBody(method)) {
			MediaType mediaType = null;
			if (headers.get("Content-Type") != null) {
				mediaType = MediaType.parse(headers.get("Content-Type"));
			}
			requestBody = RequestBody.create(mediaType, StreamUtils.copyToByteArray(inputStream));
		}

		Request.Builder builder = new Request.Builder()
				.headers(headers.build())
				.url(uri)
				.method(method, requestBody);

		Response response = httpClient.newCall(builder.build()).execute();

		LinkedMultiValueMap<String, String> responseHeaders = new LinkedMultiValueMap<>();

		for (Map.Entry<String, List<String>> entry : response.headers().toMultimap().entrySet()) {
			responseHeaders.put(entry.getKey(), entry.getValue());
		}

		this.helper.setResponse(response.code(), response.body().byteStream(),
				responseHeaders);
		context.setRouteHost(null); // prevent SimpleHostRoutingFilter from running
		return null;
    }
}
```

上面的过滤器，将Servlet请求信息转换为OkHttp3请求信息，然后执行HTTP请求，并将OkHttp3响应信息转换到Servlet响应。

### 如何写Post Filter

Post过滤器通常负责操作响应。下面的过滤器添加了一个随机`UUID`作为`X-Sample`头信息：

```java
public class AddResponseHeaderFilter extends ZuulFilter {
	@Override
	public String filterType() {
		return POST_TYPE;
	}

	@Override
	public int filterOrder() {
		return SEND_RESPONSE_FILTER_ORDER - 1;
	}

	@Override
	public boolean shouldFilter() {
		return true;
	}

	@Override
	public Object run() {
		RequestContext context = RequestContext.getCurrentContext();
    	HttpServletResponse servletResponse = context.getResponse();
		servletResponse.addHeader("X-Sample", UUID.randomUUID().toString());
		return null;
	}
}
```

> 其他处理，如转换响应体，要复杂的多，并需要大量的计算。

## Zuul Errors工作原理

如果在Zuul过滤器生命周期中的任何地方抛出异常，都会执行`error`过滤器。只有`RequestContext.getThrowable()`非空时才执行`SendErrorFilter`。然后它在请求中设置特定的`javax.servlet.error.*`属性，并将请求转发到Spring Boot错误页。

## Zuul Eager Application Context Loading

Zuul内部使用Ribbon调用远程URLs。默认情况下，Ribbon客户端在第一次调用时由Spring Cloud延迟加载。可以通过以下配置来修改Zuul的此行为，从而在应用启动时立即加载子Ribbon相关的应用上下文。下面的例子展示了如何启用即时加载：

**application.yml.**

```yaml
zuul:
  ribbon:
    eager-load:
      enabled: true
```










