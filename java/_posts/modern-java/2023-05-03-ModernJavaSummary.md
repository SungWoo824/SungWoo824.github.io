---
layout: post  
title:  "Java version 변화점 정리."
description:  
date: 2023-05-03 20:00 PM  
categories: modern-java
---

### Modern Java Summary

```java
Collections.sort(inventory, new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
})
```
위의 코드는 사과의 무게에 따라 정렬하는 코드입니다. 이를 Java 8 에서는
```java
inventory.sort(comparing(Apple::getWeight));
```
위와 같이 자연어에 더 가깝게 간단한 방식으로 코드를 구현할 수 있습니다.

Java8 에서는 간단하게 아래와 같은 새로운 기술들이 있습니다.

 * Stream API
 * 메서드에 코드를 전달하는 기법 (Method Reference, lambda expression)
 * 인터페이스의 디폴트 메서드


#### Stream Processing
Stream API 의 핵심은 기존에 한번에 한 항목을 처리하였다면,
자바 8에서는 고수준으로 추상화해서 일련의 스트림으로 만들어 처리 할수 있다는 것입니다.

#### 동작 파라미터화로 메서드에 코드 전달
메서드를 다른 메서드의 인수로 넘겨주는 기능을 제공합니다.
이러한 기능을 이론적으로 동작 파라미터화 (behavior parameterization) 라고 부릅니다.

#### 병렬성과 공유 가변 데이터
보통 다른 코드와 동시에 실행 하더라도 안전하게 실행할 수 있는 코드를 만드려면 
공유된 가변 데이터에 접근하지 않아야 한다.
이를 위해서는 기존처럼 synchronized 를 이용하여 해결할수 있지만 
다중 프로세싱 코어 환경에서 병렬이라는 목적이 무력화 되며 생각보다 위험할수 있다.
Stream API 를 이용하면 이보다 쉽게 병렬성을 이용할수 있습니다.

#### Method Reference
이 페이지의 맨 처음에 썼던 함수를 간단히 하는 부분입니다.
```java
객체 :: 메소드
```
위와 같은 형식으로 나타낼수 있습니다.
