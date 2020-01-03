---
title: 16.重写Location头信息
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Rewriting The Location Header
order: 16
---

如果Zuul面对的是web应用，当web应用通过HTTP的`3XX`状态码来重定向时，您可能需要重写`Location`头信息。否则，浏览器将重定向到web应用的URL，而不是Zuul URL。您可以配置Zuul过滤器`LocationRewriteFilter`将`Location`头信息重写为Zuul的URL。另外它还添加了特定于路由的前缀。以下示例使用Spring Configuration文件添加过滤器：

```java
import org.springframework.cloud.netflix.zuul.filters.post.LocationRewriteFilter;
...

@Configuration
@EnableZuulProxy
public class ZuulConfig {
    @Bean
    public LocationRewriteFilter locationRewriteFilter() {
        return new LocationRewriteFilter();
    }
}
```

> **警告**
> 使用这个过滤器时一定要谨慎。这个过滤器作用于所有`3XX`响应码的`Location`头信息，可能不适用于所有场景，例如将用户重定向到外部URL时。



