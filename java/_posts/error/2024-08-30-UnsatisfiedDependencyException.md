---
layout: post  
title:  "SpringBoot JPA Error."
description:  
date: 2024-07-18 11:00 AM  
categories: error
---

### org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'userDetailsServiceImpl' defined in URL ...

회원 관련 데이터를 조회하는 로직을 실행시키려 하는데 `UnsatisfiedDependencyException` 이 발생했습니다.


---

#### UnsatisfiedDependencyException

![2024_08_30_1](/assets/img/post/2024-08-30-1.png)


```
public class UnsatisfiedDependencyException extends BeanCreationException

Exception thrown when a bean depends on other beans or simple properties that were not specified in the bean factory definition, although dependency checking was enabled.
```

번역하면
- 종속성 검사가 활성화되었지만 빈이 다른 빈이나 빈 팩토리 정의에 지정되지 않은 단순 속성에 종속된 경우 발생하는 예외입니다.

---

#### 발생원인

등록된 빈을 의존성 주입등을 이용하여 사용하는 경우 발생하는 Exception 이라 파악하여 조금더 검색한 결과

- `@Autowired` 사용시 동일 타입 `Bean`이 2개 이상인 경우 등 해당하는 `Bean`을 찾지 못하는 경우 발생 
- 의존성 주입을 위하여 `@Qualifier` 어노테이션을 사용시 잘못 지정한 경우

등등 여러가지 원인으로 발생하지만 의존성 주입시 발생할수 있는 예외로 보여졌습니다.

하지만 문제 지점의 코드를 확인해 보아도 위와같은 부분은 보이지 않았습니다.


에러 코드를 다시 한번 확인 해보는데

```console
Caused by: java.lang.IllegalArgumentException: Not a managed type: class com.common.api.user.entity.User
        at org.hibernate.metamodel.model.domain.internal.JpaMetamodelImpl.managedType(JpaMetamodelImpl.java:181)
        at org.hibernate.metamodel.model.domain.internal.MappingMetamodelImpl.managedType(MappingMetamodelImpl.java:496)
        at org.hibernate.metamodel.model.domain.internal.MappingMetamodelImpl.managedType(MappingMetamodelImpl.java:99)
        at org.springframework.data.jpa.repository.support.JpaMetamodelEntityInformation.<init>(JpaMetamodelEntityInformation.java:77)
        at org.springframework.data.jpa.repository.support.JpaEntityInformationSupport.getEntityInformation(JpaEntityInformationSupport.java:69)
```

이러한 부분을 확인하였고, `User` 클래스가 Managed Type 이 아니라는 걸 확인하고 
`User` 클래스를 다시 확인하였지만 `@Entity` 어노테이션도 정상적으로 붙어있고 문제가 없어보였습니다.



문제는 생각보다 간단했는데......

```java
public interface DatasourceConfig {
    String DATA_SOURCE_SUFFIX = "DataSource";
    String ENTITY_MANAGER_SUFFIX = "EntityManager";
    String TRANSACTION_MANAGER_SUFFIX = "TransactionManager";
    String COMMON_DB_PREFIX = "common";
    String OTHER_DB_PREFIX = "other";
    
    String USER_REPOSITORY_BASE_PACKAGE = "com.common.api.user.repository";
    String USER_DOMAIN_BASE_PACKAGE = "com.common.api.user.entity.";
}
```

여러 데이터 베이스 서버로 구성이 되있어 해당 `Interface` 에 DataSource 에 접근할 패키지 경로를 설정해 두고 이를 이용해,

`DatasourceConfig` 를 설정하였는데

![2024_08_30_2](/assets/img/post/2024-08-30-2.png)

페키지 경로에 오타가 있어 해당 엔티티에 접근이 되지 않아 발생한 문제였습니다.

오타를 수정하니 정상적으로 동작하였습니다.