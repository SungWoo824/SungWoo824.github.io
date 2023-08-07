---
layout: post  
title:  Spring Kafka
description:  
date: 2023-08-05 15:00 PM  
categories: summary
---

## 스프링 카프카 (Spring Kafka)

스프링 카프카는 카프카를 스프링 프레임워크에서 효과적으로 사용할수 있도록 만들어진 라이브러리 입니다.
스프링 카프카 라이브러리는 카프카 클라이언트에서 사용하는 여러가지 패턴을 미리 제공합니다.

### 스프링 카프카 프로듀서

스프링 카프카 프로듀서는 `카프카 템플릿(Kafka Template)` 이라고 불리는 클래스를 이용하여 데이터를 전송할 수 있습니다.
카프카 템플릿은 `프로듀서 팩토리(ProducerFactory)` 클래스를 통해 생성할 수 있습니다.
스프링 카프카에서 제공하는 기본 카프카 템플릿을 사용하는 방법이 존재하고, 직접 사용자가 카프카 템플릿을 프로듀서 팩토리로 생성하는 방법이 있습니다.

#### 기본 카프카 템플릿
기본 카프카 템플릿은 기본 프로듀서 팩토리를 통해 생성된 카프카 템플릿을 사용합니다.
기본 카프카 템플릿을 사용할때는 `application.yaml` 에 프로듀서 옵션을 넣고 사용할수 있습니다.

```yaml
spring.kafka.producer.acks
spring.kafka.producer.batch-size
spring.kafka.producer.bootstrap-servers
spring.kafka.producer.buffer-memory
spring.kafka.producer.client-id
spring.kafka.producer.compression-type
spring.kafka.producer.key-serializer
spring.kafka.producer.properties.*
spring.kafka.producer.retries
spring.kafka.producer.transaction-id-prefix
spring.kafka.producer.value-serialize
```
위와같은 프로듀서 옵션값들을 설정할수 있습니다.

카프카 클라이언트에 포함된 옵션과 동일하지만, 선언하는 기준은 카프카 클라이언트에서 사용하던 규칙과는 조금 다릅니다.

- `bootstrap-servers` -> `localhost:9092`
- `key-serializer` -> `StringSerializer`
- `value-serializer` -> `StringSerializer`

스프링 카프카에서는 옵션을 설정하지 않으면 3개의 필수 옵션이 위와같이 자동으로 설정되어 실행됩니다.

`KafkaTemplate.send()` 메소드는 아래와 같이 오버로딩하는 메서드들이 있습니다.
- `send(String topic, K key, V data)` : 메시지 키, 메시지 값을 포함하여 특정 토픽으로 전달
- `send(String topic, Integer partition, K key, V data)` : 메시지 키, 메시지 값이 포함된 레코드를 특정 토픽의 특정 파티션으로 전달
- `send(String topic, Integer partition, Long timestamp, K key, V data)` : 메시지 키, 메시지 값, 타임스탬프가 포함된 레코드를 특정 토픽의 특정 파티션으로 전달
- `send(ProducerRecord<K, V> record)` : 프로듀서 레코드(ProducerRecord)객체를 전송

### 스프링 카프카 컨슈머

스프링 카프카 컨슈머는 기존 컨슈머를 2개의 타입으로 나누고 커밋을 7가지로 나누어 세분화 하였습니다.
타입은 크게 `레코드 리스너(MessageListener)` 와 `배치 리스너(BatchMessageListener)`가 있습니다.


#### 레코드 리스너(MessageListener)

레코드 리스너는 단 1개의 레코드를 처리합니다.

레코드 리스너의 종류와 메서드 파라미터는 아래와 같습니다.

<table>
    <thead>
        <th>리스너 이름</th>
        <th>상세 설명</th>
        <th>생성 메서드 파라미터</th>
    </thead>
  <tr>
    <td rowspan="2">MessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 오토 커밋 또는 컨슈머 컨테이너의 AckMode를 사용하는 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data)</td>
  </tr>
  <tr>
    <td>onMessage(V data)</td>
  </tr>
  <tr>
    <td rowspan="2">AcknowledgingMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 매뉴얼 커밋을 사용하는 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data, Acknowledgment acknowledgment)</td>
  </tr>
  <tr>
    <td>onMessage(V data, Acknowledgment acknowledgment)</td>
  </tr>
  <tr>
    <td rowspan="2">ConsumerAwareMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 컨슈머 객체를 활용하고 싶은 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data, Consumer< K, V > consumer)</td>
  </tr>
  <tr>
    <td>onMessage(V data, Consumer< K, V > consumer)</td>
  </tr>
  <tr>
    <td rowspan="2">AcknowledgingConsumerAwareMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 매뉴얼 커밋을 사용하고 컨슈머 객체를 활용하고 싶은 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data, Acknowledgment acknowledgment, Consumer< ?, ? > consumer)</td>
  </tr>
  <tr>
    <td>onMessage(V data, Acknowledgment acknowledgment, Consumer< ?, ? > consumer)</td>
  </tr>
</table>

#### 배치 리스너(BatchMessageListener)

배치 리스너는 기존 카프카 클라이언트 라이브러리의 poll() 메서드로 리턴받은 ConsumerRecords 처럼 
한번에 여러 개 레코드들을 처리할 수 있습니다.

배치 리스너의 종류와 메서드 파라미터는 아래와 같습니다.

<table>
    <thead>
        <th>리스너 이름</th>
        <th>상세 설명</th>
        <th>생성 메서드 파라미터</th>
    </thead>
  <tr>
    <td rowspan="2">BatchMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 오토 커밋 또는 컨슈머 컨테이너의 AckMode를 사용하는 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data)</td>
  </tr>
  <tr>
    <td>onMessage(List< V > data)</td>
  </tr>
  <tr>
    <td rowspan="2">BatchAcknowledgingMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 매뉴얼 커밋을 사용하는 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data, Acknowledgment acknowledgment)</td>
  </tr>
  <tr>
    <td>onMessage(List< V > data, Acknowledgment acknowledgment)</td>
  </tr>
  <tr>
    <td rowspan="2">BatchConsumerAwareMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 컨슈머 객체를 활용하고 싶은 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data, Consumer< K, V > consumer)</td>
  </tr>
  <tr>
    <td>onMessage(List< V > data, Consumer< K, V > consumer)</td>
  </tr>
  <tr>
    <td rowspan="2">AcknowledgingConsumerAwareMessageListener</td>
    <td rowspan="2">Record 인스턴스 단위로 프로세싱, 매뉴얼 커밋을 사용하고 컨슈머 객체를 활용하고 싶은 경우</td>
    <td>onMessage(ConsumerRecord< K, V > data, Acknowledgment acknowledgment, Consumer< K, V > consumer)</td>
  </tr>
  <tr>
    <td>onMessage(List< V > data, Acknowledgment acknowledgment, Consumer< K, V > consumer)</td>
  </tr>
</table>