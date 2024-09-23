---
layout: post  
title:  "SpringBoot JPA Error."
description:  
date: 2024-09-20 11:00 AM  
categories: error
---

### org.springframework.dao.InvalidDataAccessResourceUsageException: JDBC exception executing SQL

알림관련 코드를 리팩토링 하며 발생된 오류중 하나입니다.

#### 문제발생 지점

```console
org.springframework.dao.InvalidDataAccessResourceUsageException: JDBC exception executing SQL [select b1_0.cmid,b1_0.umid,b1_0.ad_flag,b1_0.attached_file from biz_msg b1_0 where b1_0.cmid=?]; SQL [n/a]

	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:256)
	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:232)
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.translateExceptionIfPossible(AbstractEntityManagerFactoryBean.java:550)
	at org.springframework.dao.support.ChainedPersistenceExceptionTranslator.translateExceptionIfPossible(ChainedPersistenceExceptionTranslator.java:61)
	at org.springframework.dao.support.DataAccessUtils.translateIfNecessary(DataAccessUtils.java:242)
	at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:152)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184)
	at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:163)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184)
	at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:97)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184)
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223)
	at jdk.proxy2/jdk.proxy2.$Proxy188.findById(Unknown Source)
	...
Caused by: org.hibernate.exception.SQLGrammarException: JDBC exception executing SQL [select b1_0.cmid,b1_0.umid,b1_0.ad_flag,b1_0.attached_file from biz_msg b1_0 where b1_0.cmid=?]
	at org.hibernate.exception.internal.SQLExceptionTypeDelegate.convert(SQLExceptionTypeDelegate.java:64)
	at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:56)
	at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:109)
	at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:95)
	at org.hibernate.sql.results.jdbc.internal.DeferredResultSetAccess.executeQuery(DeferredResultSetAccess.java:253)
	at org.hibernate.sql.results.jdbc.internal.DeferredResultSetAccess.getResultSet(DeferredResultSetAccess.java:146)
	at org.hibernate.sql.results.jdbc.internal.JdbcValuesResultSetImpl.advanceNext(JdbcValuesResultSetImpl.java:205)
	at org.hibernate.sql.results.jdbc.internal.JdbcValuesResultSetImpl.processNext(JdbcValuesResultSetImpl.java:85)
	at org.hibernate.sql.results.jdbc.internal.AbstractJdbcValues.next(AbstractJdbcValues.java:29)
	at org.hibernate.sql.results.internal.RowProcessingStateStandardImpl.next(RowProcessingStateStandardImpl.java:88)
	at org.hibernate.sql.results.spi.ListResultsConsumer.consume(ListResultsConsumer.java:183)
	at org.hibernate.sql.results.spi.ListResultsConsumer.consume(ListResultsConsumer.java:33)
	at org.hibernate.sql.exec.internal.JdbcSelectExecutorStandardImpl.doExecuteQuery(JdbcSelectExecutorStandardImpl.java:443)
	at org.hibernate.sql.exec.internal.JdbcSelectExecutorStandardImpl.executeQuery(JdbcSelectExecutorStandardImpl.java:166)
	at org.hibernate.sql.exec.internal.JdbcSelectExecutorStandardImpl.list(JdbcSelectExecutorStandardImpl.java:91)
	at org.hibernate.sql.exec.spi.JdbcSelectExecutor.list(JdbcSelectExecutor.java:31)
	... 78 more
Caused by: java.sql.SQLSyntaxErrorException: Table 'biz_msg' doesn't exist
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:121)
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122)
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeInternal(ClientPreparedStatement.java:916)
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeQuery(ClientPreparedStatement.java:972)
	at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeQuery(ProxyPreparedStatement.java:52)
	at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeQuery(HikariProxyPreparedStatement.java)
	at org.hibernate.sql.results.jdbc.internal.DeferredResultSetAccess.executeQuery(DeferredResultSetAccess.java:217)
	... 137 more

```

데이터 베이스에서 정보를 가지고 오기 위한 코드를 실행하였을때 위와 같은 오류가 발생하였습니다.

---

#### Database Table 명 및 Column 명 불일치 문제

> Root for exceptions thrown when we use a data access resource incorrectly. Thrown for example on specifying bad SQL when using a RDBMS. Resource-specific subclasses are supplied by concrete data access packages.

RDBMS를 사용할 때 잘못된 SQL을 지정하는 등, 데이터 액세스 리소스를 잘못 사용할때 발생하는 예외입니다.

다른 테이블에 접근하여 데이터를 사용하는 경우 문제가 발생하지 않는데, 해당 테이블에만 문제가 발생한 이유는

```mysql
CREATE TABLE `BIZ_MSG` (
  `CMID` varchar(32) NOT NULL,
  `UMID` varchar(32) DEFAULT NULL,
  `ATTACHED_FILE` varchar(1000) DEFAULT NULL,
  `AD_FLAG` varchar(1) DEFAULT NULL,
  PRIMARY KEY (`CMID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

위와같이 테이블 명이 대문자로 이루어져 있기 때문에 발생하였습니다.

---

#### 문제점

기존 application.yml 파일에 설정 값은

```yaml
  jpa:
    properties:
      hibernate:
        physical_naming_strategy: org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy
```

위와같이 `physical_naming_strategy` 의 값을 `org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy` 로 설정하여
CamelCase 를 SnakeCase 로 변경하였었습니다.

하지만 위의 테이블의 경우 대문자와 Underscore 모두가 존재하고 있는 상태여서 디버깅에 출력되는 SQL 을 보면

```mysql
select b1_0.cmid,b1_0.umid,b1_0.ad_flag,b1_0.attached_file from biz_msg b1_0 where b1_0.cmid=?
```

모든 대문자들이 소문자로 변경되어 있는것을 볼수 있습니다. 그렇기 때문에 MYSql 에서 해당 쿼리를 인식하지 못하는 문제가 발생했습니다.

---

#### 문제해결

먼저 `org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy` 의 동작 방식을 확인해 보았습니다.

![2024_09_20_1](/assets/img/post/2024-09-20-1.png)

각각의 케이스 별로 `apply()` 함수를 이용하여 변환하고 있는것을 확인하였습니다.

디버깅 해보니 해당 부분의 logicalName 에 Entity 의 table name 에 설정한 값이 넘어오게 되는것을 확인했습니다.


![2024_09_20_2](/assets/img/post/2024-09-20-2.png)

![2024_09_20_3](/assets/img/post/2024-09-20-3.png)


현재 모든 Entity의 table name 은 database 의 테이블 명을 직접 입력해준 상황이었기에,
테이블 명은 입력한 값을 그대로 처리하도록 하기위한 코드를 작성하였습니다.

```java
public class CustomCamelCaseToUnderscoresNamingStrategy extends CamelCaseToUnderscoresNamingStrategy {
    @Override
    public Identifier toPhysicalTableName(Identifier logicalName, JdbcEnvironment jdbcEnvironment) {
        return logicalName;
    }
}
```

특별할 작업은 없이 `CamelCaseToUnderscoresNamingStrategy` 를 상속받는 클래스를 생성한후

```yaml
  jpa:
    properties:
      hibernate:
        physical_naming_strategy: com.example.api.global.config.CustomCamelCaseToUnderscoresNamingStrategy
```

application.yml 의 설정에 해당 클래스를 지정해줬습니다.

이후 확인 결과

```mysql
select
        b1_0.cmid,
        b1_0.umid,
        b1_0.ad_flag,
        b1_0.attached_file 
    from
        BIZ_MSG b1_0 
    where
        b1_0.cmid=?
```

Table 명이 지정한대로 대문자가 변환하지 않고 쿼리가 실행되고 문제없이 동작하는것을 확인하였습니다.