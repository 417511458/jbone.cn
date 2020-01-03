---
title: Spring Cloud Netflix Zuul官方文档翻译
date: 2019-11-29 08:49:43
type: translate-spring-cloud-netflix-zuul
description: Spring Cloud Netflix Zuul官方文档翻译
keywords: Spring Cloud Netflix Zuul，Spring Cloud 网关，翻译，embedded zuul reverse proxy
order: 0
---

本专题是对Spring Cloud Netflix官方文档中Zuul部分的翻译。

> Spring Cloud Netflix Zuul版本：2.1.3.RELEASE

译文 | 原文 | 贡献者 | 状态
---|---|---|---
[1.概述](overview.html)  |  [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_router_and_filter_zuul) | [马军伟](https://github.com/417511458) | 完成
[2.如何集成Zuul](how-to-include.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_router_and_filter_zuul) | [马军伟](https://github.com/417511458) | 完成
[3.嵌入式Zuul反向代理](embedded-zuul-reverse-proxy.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#netflix-zuul-reverse-proxy) | [马军伟](https://github.com/417511458) | 完成
[4.Zuul Http Client](zuul-http-client.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_zuul_http_client) | [马军伟](https://github.com/417511458) | 完成
[5.Cookies和敏感头信息](cookies-and-sensitive-headers.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_cookies_and_sensitive_headers) | [马军伟](https://github.com/417511458) | 完成
[6.忽略头信息](ignored-headers.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_ignored_headers) | [马军伟](https://github.com/417511458) | 完成
[7.管理端点](management-endpoints.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_management_endpoints) | [马军伟](https://github.com/417511458) | 完成
[8.抑制模式和本地转发](strangulation-patterns-and-local-forwards.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_strangulation_patterns_and_local_forwards) | [马军伟](https://github.com/417511458) | 完成
[9.通过Zuul上传文件](uploading-files-through-zuul.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_uploading_files_through_zuul) | [马军伟](https://github.com/417511458) | 完成
[10.查询字符串编码](query-string-encoding.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_query_string_encoding) | [马军伟](https://github.com/417511458) | 完成
[11.请求URI编码](request-uri-encoding.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_request_uri_encoding) | [马军伟](https://github.com/417511458) | 完成
[12.纯嵌入式Zuul](plain-embedded-zuul.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_plain_embedded_zuul) | [马军伟](https://github.com/417511458) | 完成
[13.禁用Zuul Filters](disable-zuul-filter.html) | [原文地址](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.1.3.RELEASE/single/spring-cloud-netflix.html#_disable_zuul_filters) | [马军伟](https://github.com/417511458) | 完成

<br />

> `-`表示当前文章暂时没人翻译，欢迎认领自己感兴趣的专题，加入翻译！
>
> 如果您发现已翻译的文章有错或有更好的表达方式，欢迎指出！


<br />

**[我也要加入翻译>>>](/translate/join.html)**

<br />






