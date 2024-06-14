---
title: Distributed Systems - Communication Patterns
date: 2024-06-14 22:31:00 +0900
categories: [System Design, Distributed Systems]
published: true
tags:
  - System Design
  - Distributed Systems
---

분산 시스템과 다른 시스템의 주요 차이점 중 하나는 다양한 노드가 네트워크 경계를 넘어 데이터를 교환해야 한다는 점이다.

### Serialization and Deserialization#

모든 노드에는 메모리에 있는 데이터를 네트워크를 통해 전송할 수 있는 형식으로 변환하는 방법이 필요하며 네트워크에서 수신된 데이터를 다시 적절한 메모리 내의 표현으로 변환하는 방법도 필요하다. 이러한 과정을 **직렬화(Serialization)** 및 **역직렬화(Deserialization)** 하고 한다.

![image](https://miro.medium.com/v2/resize:fit:1150/0*VqYHpIfzrEpi9mp3.jpeg)

### Synchronous and asynchronous communication

노드 A가 노드 B와 동기적으로 통신하는 경우 노드 A는 후속 작업을 진행하기 전에 노드 B로부터 응답을 받을 때까지 기다려야 한다.

대신 노드 A가 비동기식으로 통신하는 경우 이는 해당 요청이 완료될 때까지 기다릴 필요가 없음을 의미한다.
동시에 다른 작업을 진행하고 수행할 수 있다.

![image](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSmPnAQrZEC1o6WJ1z09eTCACKLSnfMDRO0Ig&s)

### Datastores for Asynchronous Communication

비동기식 통신을 달성하려면 데이터 저장소에 요청을 저장해야 하며 이러한 데이터 저장소는 메시지 큐와 이벤트 로그라는 두 가지 주요 범주에 속한다.

#### Message Queue

메시지 큐 구성 방법에 따라 다음 두 가지 경우 중 하나에 해당하는 메시지가 삭제될 수 있다.

- 소비자에게 배송되자마자
  -  소비자가 메시지를 받은 후 이를 확인하기 전에 실패할 수 있으므로 본질적으로 최대 한번 보장을 제공
- 소비자가 명시적으로 확인한 후에만 메시지를 성공적으로 처리
  -  메시지가 대기열에서 제거되기 전에 적어도 단일 소비자가 메시지를 처리해야 하므로 최소 한 번 의미 체계를 제공.

대부분의 메시지 큐에는 활성 상태를 보장하는 실패한 소비자에 대처하기 위해 확인되지 않은 메시지에 대한 시간 초과 논리가 포함되어 있다.
이는 확인되지 않은 메시지가 대기열에 다시 들어가고 새 소비자에게 다시 전달됨을 의미한다.
결과적으로 메시지가 여러 소비자에게 두 번 이상 전달될 수 있는 경우가 존재한다.

![image](https://substackcdn.com/image/fetch/w_1200,h_600,c_fill,f_jpg,q_auto:good,fl_progressive:steep,g_auto/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F933ba5a8-cf94-4da2-86f2-53e2dc57d5cc_1999x1215.png)

- 대표 메세지 큐
  - ActiveMQ
  - RabbitMQ
  - Amazon SQS 

#### Event Log

이벤트 로그는 메시지 큐와 약간 다른 추상화를 제공한다.

메시지는 여전히 생성자에 의해 로그 끝에 삽입되고 순서대로 저장된다.
그러나 소비자는 메시지를 소비하려는 로그 지점을 자유롭게 선택할 수 있으며 반드시 헤드일 필요는 없다.
메시지는 일반적으로 소비자가 어디에서 소비할지 선언하는 데 사용할 수 있는 인덱스와 연결된다.

메시지 큐와의 또 다른 차이점은 로그가 일반적으로 변경 불가능하므로 메시지가 처리된 후에도 제거되지 않는다는 점이다.
대신 가비지 수집이 주기적으로 실행되어 로그 헤드에서 오래된 메시지를 제거한다.

소비자는 동일한 메시지를 두 번 처리하는 것을 방지하여 정확히 한 번 처리 의미 체계를 달성하기 위해 이미 소비한 로그 부분을 나타내는 오프셋을 추적할 책임이 있다.
이 오프셋은 앞에서 설명한 것과 유사하게 각 메시지에 대한 고유 식별자 역할을 한다.

- 대표 이벤트 로그
  - Apache Kafka
  - Amazon Kinesis
  - Azure Event Hubs 

### Communication Models

메시지 큐와 이벤트 로그를 사용하면 **지점 간 통신(point-to-point)** 과 **게시-구독(publish-subscribe)** 이라는 두 가지 서로 다른 형태의 통신을 사용할 수 있습니다 .

- 지점 간 통신(point-to-point)
  - 지점 간 모델 은 두 개의 애플리케이션만 연결해야 할 때 사용됨
- 게시-구독(publish-subscribe)
  - 게시 -구독 모델은 둘 이상의 애플리케이션이 단일 애플리케이션에서 보낸 메시지에 관심이 있을 때 사용됨

---

참고

- [Distributed Systems for Practitioners](https://www.educative.io/courses/distributed-systems-practitioners)
