---
title: 安装
description: jbone安装开发环境
keywords: jbone安装开发环境
type: guide
p: guide/installation
date: 2019-11-29 18:39:06
---

jbone是一个项目群，包含公共项目和业务线项目。

公共项目是公共jar包或各系统都要用到的项目，是必须要安装的项目，如系统管理、单点登录等；

业务线项目是为了满足某业务需求的项目，是根据个人需要选择安装的项目，如CMS、电子商城等。

> 第一次安装jbone务必按照以下方式执行，以免采坑。摸清楚之后随意。

## 安装Redis
### 下载
```shell script
wget http://download.redis.io/releases/redis-5.0.3.tar.gz
```
### 解压
```shell script
tar xzf redis-5.0.3.tar.gz
```
### 安装
```shell script
cd redis-5.0.3
make
```
### 配置
在`redis.conf`中加入以下配置：

```shell script
requirepass jbone
daemonize yes
```
### 启动
```shell script
cd redis-5.0.3/src/
./redis-server ../redis.conf
```
## 安装RabbitMq
### 安装
```shell script
brew install rabbitmq
```
### 启动
```shell script
/usr/local/Cellar/rabbitmq/3.7.10/sbin
./rabbitmq-server -detached
```
## 安装Nginx
### 安装
```shell script
brew install nginx
```
### 配置
在nginx.conf中加入以下配置：

```shell script
upstream register {       
      server 127.0.0.1:10001;
}
upstream smadmin {
      server 127.0.0.1:10002;
}
upstream gateway {
      server 127.0.0.1:10005;
}
upstream sso {
      server 127.0.0.1:30001;
}
upstream ssomanagement {
      server 127.0.0.1:30002;
}
upstream sysadmin {
      server 127.0.0.1:20002;
}
upstream cmsadmin {
      server 127.0.0.1:50002;
}
upstream cmsportal {
      server 127.0.0.1:50003;
}
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  sso.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://sso/;
             proxy_redirect off;
             root index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  ssomanagement.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://ssomanagement/;
             proxy_redirect off;
             root index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  gateway.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://gateway/;
             proxy_redirect off;
             root index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  smadmin.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://smadmin/;
             proxy_redirect off;
             root index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  sysadmin.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://sysadmin/;
             proxy_redirect off;
             root index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  cmsadmin.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;      
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://cmsadmin/;
             proxy_redirect off;
             root index.html;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  cmsportal.local.jbone.cn;
        location / {
             proxy_set_header Host $host;
             proxy_set_header X-Real-Ip $remote_addr;
             proxy_set_header X-Forwarded-For $remote_addr;
             proxy_pass http://cmsportal/;
             proxy_redirect off;
             root index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
server {
        listen       80;
        server_name  register.local.jbone.cn;
        

        location / {
             proxy_pass http://register;
             proxy_redirect off;
             root index.html;
        }

        location = /50x.html {
            root   html;
        }

    }
```

### 启动
```shell script
./nginx
```

## 配置本地Host
```properties
127.0.0.1 sso.local.jbone.cn
127.0.0.1 ssomanagement.local.jbone.cn
127.0.0.1 sysadmin.local.jbone.cn
127.0.0.1 cmsadmin.local.jbone.cn
127.0.0.1 cmsportal.local.jbone.cn
127.0.0.1 smadmin.local.jbone.cn
127.0.0.1 gateway.local.jbone.cn
127.0.0.1 register.local.jbone.cn
127.0.0.1 zipkinserver.local.jbone.cn
127.0.0.1 smmonitor.local.jbone.cn
```


## 安装公共项目

### 下载源码

```shell script
git clone https://github.com/417511458/jbone.git
git clone https://github.com/417511458/jbone-system.git
git clone https://github.com/417511458/jbone-system-admin.git
git clone https://github.com/417511458/jbone-sso.git
git clone https://github.com/417511458/jbone-service-management.git
```

### 导入IDE
将下载的源码导入到自己的IDE中

### install
按以下顺序一次安装：

* jbone
* jbone-system
* jbone-sso

```shell script
mvn install
```

> 注意：公共jar项目都发布到maven中央仓库了，如果不修改公共jar包，可以不在本地install。

## 本地启动
按以下顺序来启动项目：
1. 先启动Mysql、Redis、RabbitMq、Nginx等;
2. 启动服务治理部分;
3. 启动其他项目。

### 启动服务注册中心
本地运行`jbone-sm-register`的启动类，或放在`tomcat`中运行。
### 启动系统管理服务
本地运行`jbone-system-server`的启动类，或放在`tomcat`中运行。
### 启动SSO
放在`tomcat`中运行。

注意`sso-server`的端口号为`30001`，`sso-management`的端口号为`30002`。

### 启动系统管理后台
本地运行`jbone-system-admin`的启动类，或放在`tomcat`中运行。
### 访问测试
在浏览器访问`http://sysadmin.local.jbone.cn`，重定向到登录页面。

输入默认用户名密码`jbone/jbone`，登录成功后跳转到系统页面，即为成功。

