---
title: SpringBoot常用配置
date: 2018-03-21 14:35:32
tags: [springboot]
---

SpringBoot常用配置
---

### 1. SPRING CONFIG

```properties
spring.config.name    
# 配置文件名称，默认为application 
spring.config.location  
#配置文件存放位置，默认为classpath目录下
```

### 2.mvc

```properties
spring.mvc.async.request-timeout 
#设定async请求的超时时间，以毫秒为单位，如果没有设置的话，以具体实现的超时时间为准，比如tomcat的servlet3的话是10秒. 
spring.mvc.date-format = yyyy-MM-dd HH:mm:ss
#设定日期的格式，比如dd/MM/yyyy或者yyyy-MM-dd HH:mm:ss
spring.mvc.favicon.enabled 
#是否支持favicon.ico，默认为: true 
spring.mvc.ignore-default-model-on-redirect 
#在重定向时是否忽略默认model的内容，默认为true 
spring.mvc.locale 
#指定使用的Locale. 
spring.mvc.message-codes-resolver-format 
#指定message codes的格式化策略(PREFIX_ERROR_CODE,POSTFIX_ERROR_CODE). 
spring.mvc.view.prefix 
#指定mvc视图的前缀. 
spring.mvc.view.suffix 
#指定mvc视图的后缀.
```

### 3.view

```properties
spring.view.prefix 
#设定mvc视图的前缀. 
spring.view.suffix 
#设定mvc视图的后缀.
```

### 4.resource

```properties
spring.resources.add-mappings 
#是否开启默认的资源处理，默认为true 
spring.resources.cache-period 
#设定资源的缓存时效，以秒为单位. 
spring.resources.chain.cache 
#是否开启缓存，默认为: true 
spring.resources.chain.enabled 
#是否开启资源 handling chain，默认为false 
spring.resources.chain.html-application-cache 
#是否开启h5应用的cache manifest重写，默认为: false 
spring.resources.chain.strategy.content.enabled 
#是否开启内容版本策略，默认为false 
spring.resources.chain.strategy.content.paths 
#指定要应用的版本的路径，多个以逗号分隔，默认为:[/**] 
spring.resources.chain.strategy.fixed.enabled 
#是否开启固定的版本策略，默认为false 
spring.resources.chain.strategy.fixed.paths 
#指定要应用版本策略的路径，多个以逗号分隔 
spring.resources.chain.strategy.fixed.version 
#指定版本策略使用的版本号 
spring.resources.static-locations 
#指定静态资源路径，默认为classpath:[/META-INF/resources/,/resources/, /static/, /public/]以及context:/
```

### 5.multipart

```properties
multipart.enabled 
#是否开启文件上传支持，默认为true 
multipart.file-size-threshold 
#设定文件写入磁盘的阈值，单位为MB或KB，默认为0 
multipart.location 
#指定文件上传路径. 
multipart.max-file-size 
#指定文件大小最大值，默认1MB 
multipart.max-request-size 
#指定每次请求的最大值，默认为10MB
```

### 6.freemarker

```properties
spring.freemarker.allow-request-override 
#指定HttpServletRequest的属性是否可以覆盖controller的model的同名项 
spring.freemarker.allow-session-override 
#指定HttpSession的属性是否可以覆盖controller的model的同名项 
spring.freemarker.cache 
#是否开启template caching. 
spring.freemarker.charset 
#设定Template的编码. 
spring.freemarker.check-template-location 
#是否检查templates路径是否存在. 
spring.freemarker.content-type 
#设定Content-Type. 
spring.freemarker.enabled 
#是否允许mvc使用freemarker. 
spring.freemarker.expose-request-attributes 
#设定所有request的属性在merge到模板的时候，是否要都添加到model中. 
spring.freemarker.expose-session-attributes 
#设定所有HttpSession的属性在merge到模板的时候，是否要都添加到model中. 
spring.freemarker.expose-spring-macro-helpers 
#设定是否以springMacroRequestContext的形式暴露RequestContext给Spring’s macro library使用 
spring.freemarker.prefer-file-system-access 
#是否优先从文件系统加载template，以支持热加载，默认为true 
spring.freemarker.prefix 
#设定freemarker模板的前缀. 
spring.freemarker.request-context-attribute 
#指定RequestContext属性的名. 
spring.freemarker.settings 
#设定FreeMarker keys. 
spring.freemarker.suffix 
#设定模板的后缀. 
spring.freemarker.template-loader-path 
#设定模板的加载路径，多个以逗号分隔，默认: [“classpath:/templates/”] 
spring.freemarker.view-names 
#指定使用模板的视图列表.
```

### 7.http

```properties
spring.hateoas.apply-to-primary-object-mapper 
#设定是否对object mapper也支持HATEOAS，默认为: true 
spring.http.converters.preferred-json-mapper 
#是否优先使用JSON mapper来转换. 
spring.http.encoding.charset 
#指定http请求和相应的Charset，默认: UTF-8 
spring.http.encoding.enabled 
#是否开启http的编码支持，默认为true 
spring.http.encoding.force 
#是否强制对http请求和响应进行编码，默认为true
```

### 8.json

```properties
spring.jackson.date-format 
#指定日期格式，比如yyyy-MM-dd HH:mm:ss，或者具体的格式化类的全限定名 
spring.jackson.deserialization 
#是否开启Jackson的反序列化 
spring.jackson.generator 
#是否开启json的generators. 
spring.jackson.joda-date-time-format 
#指定Joda date/time的格式，比如yyyy-MM-dd HH:mm:ss). 如果没有配置的话，dateformat会作为backup 
spring.jackson.locale 
#指定json使用的Locale. 
spring.jackson.mapper 
#是否开启Jackson通用的特性. 
spring.jackson.parser 
#是否开启jackson的parser特性. 
spring.jackson.property-naming-strategy 
#指定PropertyNamingStrategy (CAMEL_CASE_TO_LOWER_CASE_WITH_UNDERSCORES)或者指定PropertyNamingStrategy子类的全限定类名. 
spring.jackson.serialization 
#是否开启jackson的序列化. 
spring.jackson.serialization-inclusion 
#指定序列化时属性的inclusion方式，具体查看JsonInclude.Include枚举. 
spring.jackson.time-zone 
#指定日期格式化时区，比如America/Los_Angeles或者GMT+10.
```

#### 9.LOGGING

```properties
logging.path=/var/logs 
logging.file=myapp.log 
logging.config= # location of config file (default classpath:logback.xml for logback) 
logging.level.*= # levels for loggers, e.g. “logging.level.org.springframework=DEBUG” (TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF)
```

#### 10.IDENTITY (ContextIdApplicationContextInitializer)

```properties
spring.application.name= 
spring.application.index=
```

#### 11.EMBEDDED SERVER CONFIGURATION (ServerProperties)

```properties
server.port=8080 
server.address= # bind to a specific NIC 
server.session-timeout= # session timeout in seconds 
server.context-parameters.*= # Servlet context init parameters, e.g. server.context-parameters.a=alpha 
server.context-path= # the context path, defaults to ‘/’ 
server.servlet-path= # the servlet path, defaults to ‘/’ 
server.ssl.enabled=true # if SSL support is enabled 
server.ssl.client-auth= # want or need 
server.ssl.key-alias= 
server.ssl.ciphers= # supported SSL ciphers 
server.ssl.key-password= 
server.ssl.key-store= 
server.ssl.key-store-password= 
server.ssl.key-store-provider= 
server.ssl.key-store-type= 
server.ssl.protocol=TLS 
server.ssl.trust-store= 
server.ssl.trust-store-password= 
server.ssl.trust-store-provider= 
server.ssl.trust-store-type= 
server.tomcat.access-log-pattern= # log pattern of the access log 
server.tomcat.access-log-enabled=false # is access logging enabled 
server.tomcat.internal-proxies=10\.\d{1,3}\.\d{1,3}\.\d{1,3}|\ 
192\.168\.\d{1,3}\.\d{1,3}|\ 
169\.254\.\d{1,3}\.\d{1,3}|\ 
127\.\d{1,3}\.\d{1,3}\.\d{1,3} # regular expression matching trusted IP addresses 
server.tomcat.protocol-header=x-forwarded-proto # front end proxy forward header 
server.tomcat.port-header= # front end proxy port header 
server.tomcat.remote-ip-header=x-forwarded-for 
server.tomcat.basedir=/tmp # base dir (usually not needed, defaults to tmp) 
server.tomcat.background-processor-delay=30; # in seconds 
server.tomcat.max-http-header-size= # maximum size in bytes of the HTTP message header 
server.tomcat.max-threads = 0 # number of threads in protocol handler 
server.tomcat.uri-encoding = UTF-8 # character encoding to use for URL decoding
```