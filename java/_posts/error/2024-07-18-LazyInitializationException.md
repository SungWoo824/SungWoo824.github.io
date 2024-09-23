---
layout: post  
title:  "SpringBoot JPA Error."
description:  
date: 2024-07-18 11:00 AM  
categories: error
---

### org.hibernate.LazyInitializationException: could not initialize proxy

기존 코드를 API 로 리팩토링 하는 과정중 발생한 오류입니다.

#### 문제발생 지점

```java
import java.util.List;

public class GetUserCartData {
    private final UserAdapter userAdapter;
    private final CartAdapter cartAdapter;

    public ResponseMessage getData(String userId, Long orderNo) {
        User user = userAdapter.getUserData(userId);
        List<Cart> order = cartAdapter.getCartDataListByUserNo(user.getUserNo());
    }
}


public class UserAdapter {
    private final UserRepository userRepository;
    
    @Transactional
    public User getUserData(String userId) {
        return userRepository.getReferenceById(userId);
    }
}

public class CartAdapter {
    private final CartRepository cartRepository;

    @Transactional
    public User getCartDataListByUserNo(Long userNo) {
        return cartRepository.getReferenceById(userNo);
    }
}
```

위와 비슷한 코드를 진행하였을때 `LazyInitializationException` 이 발생하였습니다.

문제를 처리하기 위해 해당 Exception 에 관련하여 확인해 보았습니다.  

---

#### getReferenceById() Method 사용시 발생하는 문제.

LazyInitializationException 의 경우 JPA 를 사용하면서 마주치는 Exception 중에 하나입니다.


```console

could not initialize proxy [com.example.api.domain.Cart#1] - no Session
org.hibernate.LazyInitializationException: could not initialize proxy
[com.example.api.domain.Order#1] - no Session

```

Project 에 JPA를 이용하는경우 Hibernate의 Lazy Loading을 사용하게되는데,
그중 getReferenceById() 함수에서 발생할수 있는 예외입니다.

일단 `getReferenceById()` 함수의 동작 방식부터 알아야 합니다.

해당 함수와 ~~`getById()`~~(Deprecated) 함수의 경우 **지연 로딩 (Lazy Loading)**으로
호출 시점이 아닌 엔티티 내부의 값이 필요해지는 시점에 DB를 조회해 값을 가지고 오도록 동작합니다.

다시말해 영속성 컨텍스트에서 명시적으로 사용하기 전까지는 엔티티를 로드하지 않고 참조값(Proxy 객체) 만을 가지고 있고,
트랜젝션 밖에서 해당 객체는 LazyInitializationException 이 발생하게 됩니다.

---

#### 문제해결

위에 찾아본 내용을 기반으로 해결할 방법은 여러개가 존재할거 같았습니다.

1. `UserAdapter` 에서 return 해줄때 필요한 데이터 값만을 반환시키도록 처리.
2. `getReferenceById()` 같은 Proxy 객체를 사용하지 않는 함수를 사용
3. 문제가 발생한 두가지의 함수를 하나의 Transaction 으로 처리

이중 3번을 선택하여 처리하였습니다.

간단하게 `GetUserCartData` 의 `getData()` 함수에 Transactional 처리를 하여 문제를 해결하였습니다. 

```java
import java.util.List;

public class GetUserCartData {
    private final UserAdapter userAdapter;
    private final CartAdapter cartAdapter;

    @Transactional
    public ResponseMessage getData(String userId, Long orderNo) {
        User user = userAdapter.getUserData(userId);
        List<Cart> order = cartAdapter.getCartDataListByUserNo(user.getUserNo());
    }
}
```