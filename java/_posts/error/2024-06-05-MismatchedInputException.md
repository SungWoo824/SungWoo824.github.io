---
layout: post  
title:  "SpringBoot JPA Error."
description:  
date: 2024-06-05 11:00 AM  
categories: error
---

### com.fasterxml.jackson.databind.exc.MismatchedInputException: raw timestamp (1722415163000) not allowed for `java.time.LocalDateTime`: need additional information such as an offset or time-zone (see class Javadocs)

>General exception type used as the base class for all JsonMappingExceptions that are due to input not mapping to target definition; these are typically considered "client errors" since target type definition itself is not the root cause but mismatching input. This is in contrast to InvalidDefinitionException which signals a problem with target type definition and not input.
>This type is used as-is for some input problems, but in most cases there should be more explicit subtypes to use.

>NOTE: name chosen to differ from `java.util.InputMismatchException` since while that would have been better name, use of same overlapping name causes nasty issues with IDE auto-completion, so slightly less optimal chosen.

Debezium Connector 가 적제한 Kafka Topic 에서 데이터를 가지고 와 처리를 하는 부분에서 발생한 오류입니다.


---

#### MismatchedInputException

위의 `MismatchedInputException` 에 대한 참조글처럼 입력된 값을 역질렬화 될 타입과 맞지 않는 경우 발생합니다.

```console
Caused by: com.fasterxml.jackson.databind.exc.MismatchedInputException: raw timestamp (1722415163000) not allowed for `java.time.LocalDateTime`: need additional information such as an offset or time-zone (see class Javadocs)
 at [Source: UNKNOWN; byte offset: #UNKNOWN] (through reference chain: com.example.api.product.entity.Deal["expired_at"])
	at com.fasterxml.jackson.databind.exc.MismatchedInputException.from(MismatchedInputException.java:63)
	at com.fasterxml.jackson.databind.DeserializationContext.reportInputMismatch(DeserializationContext.java:1733)
	at com.fasterxml.jackson.datatype.jsr310.deser.JSR310DateTimeDeserializerBase._throwNoNumericTimestampNeedTimeZone(JSR310DateTimeDeserializerBase.java:180)
	at com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer.deserialize(LocalDateTimeDeserializer.java:140)
	at com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer.deserialize(LocalDateTimeDeserializer.java:40)
	at com.fasterxml.jackson.databind.deser.impl.FieldProperty.deserializeAndSet(FieldProperty.java:138)
	at com.fasterxml.jackson.databind.deser.BeanDeserializer.deserializeFromObject(BeanDeserializer.java:392)
	at com.fasterxml.jackson.databind.deser.BeanDeserializer.deserialize(BeanDeserializer.java:185)
	at com.fasterxml.jackson.databind.ObjectMapper._convert(ObjectMapper.java:4444)
	... 72 more
```

---

#### 발생원인

Connector 에서 Topic 에 데이터 저장시 Long Type 으로 저장이 되는 경우가 있고, 날짜 포멧으로 저장이 되는 경우를 확인했습니다.

확인해보니 Database 의 Column Type 이 DateTime 인경우 Long 의 형태로, Timestamp 의 경우 날짜 포멧으로 저장이 되고있었습니다.

---

#### 문제해결

위와 같은 컬럼중에서 오류가 발생하는 부분은 DateTime 이었고 DB의 해당 컬럼을 timestamp 로 변경해주었습니다.

그럼에도 문제가 해결되지 않고 오류가 발생하였는데, 저장되있는 값들을 확인해보니 DateTime 이었던 컬럼의 데이터중 
0000-00-00 으로 저장되어 있는 데이터들이 있었고, 해당 데이터가 문제가 되고 있었습니다.

해당 데이터들을 `NULL` 로 UPDATE 해주며 해당 문제는 해결이 되었습니다.