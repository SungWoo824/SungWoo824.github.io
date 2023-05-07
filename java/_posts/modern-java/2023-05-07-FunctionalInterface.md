---
layout: post  
title:  "Java8 Functional Interface."
description:  
date: 2023-05-03 20:00 PM  
categories: modern-java
---

### Java 8 Functional Interface

함수형 인터페이스는 오직 하나의 추상 메서드를 지정합니다. 추상 메서드는 람다 표현식의 
시그니처를 묘사하고, 이를 함수 디스크립터(function descriptor) 라고 합니다.

```java
@FunctionalInterface
interface TestInterface<T> {
    T myCall();
    
    default void printDefault() {
        System.out.println("Default function");
    }
    
    static void printStatic() {
        System.out.println("Static function");
    }
}
```
위 인터페이스는 함수형 인터페이스입니다.

하나의 추상 메서드가 존재하는 인터페이스 이므로 `default` `static` 메소드는 존재해도 됩니다.

`@FunctionalInterface` 어노테이션은 없어도 동작하고 문제가 없지만, 
해당 형식에 맞지 않는다면 아래와 같은 오류를 출력하므로, 검증 및 유지보수를 위해 붙여주는게 좋습니다.
```
Multiple non-overriding abstract methods found in interface com.practice.notepad.CustomFunctionalInterface
```

해당 인터페이스를 아래와 같이 사용할수 있습니다.

```java
TestInterface<String> testInterface = () -> "Functional";
String test = testInterface.myCall();

System.out.println();
testInterface.printDefault();
testInterface.printStatic();
```
위의 코드를 실행한다면 다음과 같이 출력 됩니다.
```console
Functional
Default function
Static function
```

---

Java 8 에서는 `java.util.function` 패키지로 여러 인터페이스를 지원합니다.

아래와 같은 인터페이스를 지원해 줍니다.

| 함수형 인터페이스             | 함수 티스크립터          | 기본형 특화                                                                                                                                                                                                                                                                        |
|-----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Predicate\<T\>        | T -> boolean      | IntPredicate, <br/>LongPredicate, <br/>DoublePredicate                                                                                                                                                                                                                        |
| Consumer\<T\>         | T -> void         | IntConsumer, <br/>LongConsumer, <br/>DoubleConsumer                                                                                                                                                                                                                           |
| Function\<T, R\>      | T -> R            | IntFunction\<R\>, IntToDoubleFunction, IntToLongFunction, <br/>LongFunction\<R\>, LongToDoubleFunction, LongToIntFunction, <br/>DoubleFunction\<R\>, DoubleToIntFunction, DoubleToLongFunction, <br/>ToIntFunction\<T\>, <br/>ToDoubleFunction\<T\>, <br/>ToLongFunction\<T\> |
| Supplier\<T\>         | () -> T           | BooleanSupplier, <br/>IntSupplier, <br/>LongSupplier, <br/>DoubleSupplier                                                                                                                                                                                                     |
| UnaryOperator\<T\>    | T -> T            | IntUnaryOperator, <br/>LongUnaryOperator, <br/>DoubleUnaryOperator                                                                                                                                                                                                            |
| BinaryOperator\<T\>   | (T, T) -> T       | IntBinaryOperator, <br/>LongBinaryOperator, <br/>DoubleBinaryOperator                                                                                                                                                                                                         |
| BiPredicate\<L, R\>   | (T, U) -> boolean |                                                                                                                                                                                                                                                                               |
| BiConsumer\<T, U\>    | (T, U) -> void    | ObjIntConsumer\<T\>, <br/>ObjLongConsumer\<T\>, <br/>ObjDoubleConsumer\<T\>                                                                                                                                                                                                   |
| BiFunction\<T, U, R\> | (T, U) -> R       | ToIntBiFunction\<T, U\>, <br/>ToLongBiFunction\<T, U\>, <br/>ToDoubleBiFunction\<T, U\>                                                                                                                                                                                                         |
