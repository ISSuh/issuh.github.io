---
title: Distributed Systems - ACID Transactions
date: 2024-06-11 22:22:00 +0900
categories: [System Design, Distributed Systems]
published: true
tags:
  - System Design
  - Distributed Systems
---

### ACID Transactions

**ACID** 는 트랜잭션 예상 동작에 대한 보장을 제공하는 데이터베이스의 트랜잭션의 속성 집합니다.
이러한 ACID에 대한 내용을 분산 시스템에도 어느정도 대입할 수 있다.

#### Atomicity (A)

원자성은 여러 작업으로 구성된 트랜잭션이 단일 단위로 처리되도록 보장하며, 이는 트랜잭션의 모든 작업이 수행되거나 아무 것도 수행되지 않음을 의미한다.

이러한 원자성 개념은 시스템의 여러 노드에서 동일한 작업을 실행해야 하는 분산 시스템으로 확장된다. 즉, 작업은 모든 노드에 대해 실행되거나 전혀 실행되지 않아야 한다.

#### Consistency (C)

일관성은 데이터베이스 불변성을 유지하면서 트랜잭션이 데이터베이스를 하나의 유효한 상태에서 다른 유효한 상태로만 전환하는 것을 보장한다.

#### Isolation (I)

격리는 트랜잭션이 동시에 실행되고 데이터 종속성이 있더라도 그 결과가 마치 트랜잭션 중 하나가 한 번에 실행되고 트랜잭션 간에 간섭이 없는 것처럼 보장한다.

#### Durability (D)

내구성은 한번 커밋된 트랜잭션이 실패하더라도 커밋된 상태를 유지하도록 보장한다.

분산 시스템의 맥락에서 이는 트랜잭션이 여러 노드에 지속적으로 저장되어야 함을 의미한다.
이는 저장 시설과 함께 노드 전체에 장애가 발생하는 경우에도 복구가 가능하게 한다.

---

참고

- [Distributed Systems for Practitioners](https://www.educative.io/courses/distributed-systems-practitioners)
