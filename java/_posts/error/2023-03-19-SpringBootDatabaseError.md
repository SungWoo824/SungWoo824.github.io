---
layout: post  
title:  "SpringBoot Database Error."
description:  
date: 2023-03-18 16:30 PM  
categories: error
---

### SpringBoot Database 설정 관련 Error 발생 원인 및 해결

#### Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

최초 프로젝트 생성후 Database 연결을 설정하지 않으면 해당 오류가 발생합니다.

```console
***************************
APPLICATION FAILED TO START
***************************

Description:

Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

Reason: Failed to determine a suitable driver class


Action:

Consider the following:
	If you want an embedded database (H2, HSQL or Derby), please put it on the classpath.
	If you have database settings to be loaded from a particular profile you may need to activate it (no profiles are currently active).
```

application.yml 또는 application.properties 파일에 DB 연결 정보를 설정해 주면 해당 오류는 해결됩니다.


```console
org.springframework.beans.factory.BeanCreationException: 
    Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: 
        Failed to instantiate [org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean]: 
            Factory method 'entityManagerFactory' threw exception with message: Driver com.mysql.cj.jdbc.Driver claims to not accept jdbcUrl, 
	at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:657) ~[spring-beans-6.0.6.jar:6.0.6]
```
#### Error creating bean with name 'entityManagerFactory' defined in class path resourc 에러

위에서 설정한 application.yml 파일의 DB의 정보가 잘못된 경우 발생하는 오류입니다.

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: localhost:3306
    username: root
    password: 1234

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306
    username: root
    password: 1234
```

DB url 이 잘못 되어있어 해당부분을 수정하면 정상적으로 동작합니다.
