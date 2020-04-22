---
layout: post
title:  "Lombok 어노테이션"
description: Lombok @(어노테이션) 정리
date:   2020-04-20 +1700
categories: Spring Lombok
---
<p>
 맨처음 공부를 시작한뒤 Getter Setter toString 을 열심히 만들다가 Lombok의 존재를 알게되었다.
</p>
단순히 어노테이션 하나를 붙임으로써 여러가지를 자동으로 생성해주다 보니 편하여 자주 쓰게 되었는데 상황에 맞는 어노테이션을 사용하기위해 정리를 하게 되었다.


## 생성자 자동 생성 어노테이션
- **@NoArgsConstructor** : 파라미터가 없는 기본 생성자 생성
- **@AllArgsConstructor** : 모든 필드 값 파라미터로 받는 생성자 생성
- **@RequiredArgsConstructor** : final 인 필드 값들을 자동으로 생성자에 파라미터 추가 생성(필수 파라미터값만)

## @Data 어노테이션
- **@Getter** : Getter 메소드 자동 생성
- **@Setter** : Setter 메소드 자동 생성
- **@ToString** : 모든 필드를 출력하는 toString() 메소드 생성
- **@EqualsAndHashCode** : hashcode 와 equals 메소드 생성

#### **@Data** 어노테이션은

 **@Getter**,**@Setter**,**@ToString**,**@EqualsAndHashCode**,**@RequiredArgsConstructor** 어노테이션을 한번에 처리한다.
 
## @Builder
 
 **@Builder** 어노테이션 같은 경우 다수의 필드를 가지고 있는 클래스인 경우 생성자 대신 빌더를 사용해 편하게 사용할수 있다
 
#### [선언 예제]
 ````java
@Builder
public class User{
    private Long Id;
    private String userName;
    private String userEmail;
}
````

#### [사용 예제]
````java
User user = User.builder()
    .id(1)
    .userName("SW")
    .userName("SW@gmail.com")
    .build();
````

## @Singular
**@Singular** 어노테이션은 컬랙션으로 된 필드에 값을 하나씩 넘겨 줄 수 있다.

#### [선언 예제]

 ````java
@Builder
public class User{
    private Long Id;
    private String userName;
    private String userEmail;
    @Singular
    private List<Integer> coupon;
}
````

#### [사용 예제]

````java
User user = User.builder()
    .id(1)
    .userName("SW")
    .userName("SW@gmail.com")
    .coupon(8000)
    .coupon(4000)
    .coupon(6000)
    .build();
````

<p>
없어도 코드를 짜는데 문제가 없지만, 이전과 비교해 한눈에 확인이 되는거 같고 반복작업을 줄여주기 때문에 편리하게 사용중에있다.
<br>
위에 사용한 어노테이션 뿐만 아니라 다양한 어노테이션이 있으므로 추후 추가하도록 하겠다.
</p>