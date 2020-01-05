---
title: 17.启用跨源请求
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Zuul官方文档翻译
keywords: Spring Cloud Zuul，Spring Cloud 网关，Enabling Cross Origin Requests
order: 17
---

默认情况下，Zuul会将所有跨源请求(`CORS`)路由到服务中。如果您想修改Zuul的这一默认处理请求方式，您可以通过自定义`WebMvcConfigurer`来实现：

```java
@Bean
public WebMvcConfigurer corsConfigurer() {
    return new WebMvcConfigurer() {
        public void addCorsMappings(CorsRegistry registry) {
            registry.addMapping("/path-1/**")
                    .allowedOrigins("https://allowed-origin.com")
                    .allowedMethods("GET", "POST");
        }
    };
}
```

以上示例中，我们允许来自`https://allowed-origin.com`的`GET`和`POST`方法向`path-1`开头的端点发送跨域请求。您可以将`CORS`配置应用于特定的路径模式，或者使用`/**`将`CORS`配置应用于整个应用程序。您可以基于此配置来自定义属性：`allowedOrigins`,`allowedMethods`,`allowedHeaders`,`exposedHeaders`,`allowCredentials`和`maxAge`。



