---
title: Distributed Systems - CAP
date: 2024-06-11 22:32:00 +0900
categories: [System Design, Distributed Systems]
published: true
tags:
  - System Design
  - Distributed Systems
---

### The CAP Theorem

CAP 정리는 분산시스템 분야에서 가장 기본적인 정리중 하나이다. 이는 분산 시스템 설계의 본질적인 균형을 설명한다.

- 일관성(Consistency)
  - 모든 읽기 요청이 마지막 쓰기 요청의 결과를 수신함
- 가용성(Availability)
  - 모든 요청이 마지막 쓰기 요청을 반영하는지 여부는 보장하지 않지만 오류가 아닌 응답을 수신함
- 파티션 허용치(Partition Tolerance)
  - 네트워크 단절 및 오류로 인해 네트워크에서 임의의 메세지들이 삭제되더라도 시스템이 지속적으로 작동해야 함

![image](https://miro.medium.com/v2/resize:fit:850/1*85GfneMfKiBphYnr_VWPBg.png)

현실에서는 항상 네트워크 단절의 위험이 존재하기 때문에 시스템 설계자가 **가용성**와 **일관성** 사이에서 적절한 균형을 이루도록 적절한 시스템을 선택해야 한다.

#### 대기시간과 일관성 사이의 절충

정상 작동중에 네트워크 파티션이 없다면 대기시간과 일관성 사이에 서로 다른 균형이 유지된다.

데이터 일관성을 보장하기 위해 시스템은 데이터가 시스템 전체에 성공적으로 전파될 때까지 쓰기 작업을 지연해야 하므로 지연 시간 이 발생한다.

이러한 절충안의 예로는 primary-backup replication 체계가 있다. 이 설정에서 동기식 복제 접근 방식은 대기 시간 보다 일관성을 선호하며 비동기 복제는 일관성을 희생하면서 대기 시간을 줄인다.

#### PACELC 정리

PACELC 정리는 CAP 정리의 확장이며 다음과 같이 설명된다.

> 네트워크 분할(P) 의 경우 시스템은 가용성(A)과 일관성(C) 중 하나를 선택해야 하지만, 그렇지 않은 경우(E), 즉 네트워크 분할 없이 시스템이 정상적으로 작동하는 경우에 시스템은 대기 시간(L)과 일관성(C) 중 하나를 선택해야 한다.

![image](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/PACELC_theorem.png/400px-PACELC_theorem.png)

### Consistency Models

CAP 정리 에 따르면 일관성은 모든 성공적인 읽기 요청이 가장 최근 쓰기 결과를 반환한다는 것을 의미한다.
그러나 이는 일관성의 형태가 다양하기 때문에 지나치게 단순화된 표현이다.

현실에서의 여러 일관성의 형태를 정확하게 정의하려면 형식 모델을 정의하고 이를 분산 시스템의 동작에 반영해야 한다.

현대 시스템 설계 분야에는 다양한 일관성 모델이 존재하지만, 그중 다음과 같은 일관성 모델을 확인한다.

- 선형성(Linearizability)
- 순차적 일관성(Sequential Consistency)
- 인과적 일관성(Causal Consistency)
- 최종 일관성(Eventual Consistency)

#### 선형성(Linearizability)

선형성 모델을 지원하는 시스템은 작업이 외부 클라이언트에게 즉각적으로 나타나는 시스템이다.

기본 아이디어는 시스템에 데이터 복사본이 하나만 있고 시스템에 대한 모든 작업이 원자적으로 수행되는 것처럼 보이게 만드는 것이다.

흔히 중앙 집중식 시스템에서 이러한 선형성이 나타난다.

![image](https://miro.medium.com/v2/resize:fit:1358/1*5fwC427YUSTm6u9bk3VWrw.png)

그러나 분산 시스템에서는 이러한 선형성이 명확하게 정의 될수 없다.
분산 시스템에서는 요청에 대한 결과를 각 노드로 업데이트 할때 네트워크의 지연과 같은 이유로 결과의 반영이 지연될 수 있기 때문이다.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*liMrf1G-LlX1A7unNUaW1g.png)

동기식 복제 기술을 사용하면 시스템을 어느정도 선형화할 수 있다.
그러나 이는 새 값이 나머지 노드에 전파될 때까지 첫 번째 쓰기 작업이 더 오래 걸린다는 것을 의미한다.

#### 순차적 일관성(Sequential Consistency)

순차 일관성은 작업이 호출되기 전이나 완료 후에 적용되도록 허용되는 약한 일관성 모델이다.

결과적으로 실시간 보장을 제공하지 않는다.
그러나 다른 클라이언트의 작업은 다른 모든 클라이언트에서 동일한 순서로 표시되어야 하며 모든 단일 클라이언트의 작업은 해당 프로그램에서 지정한 순서(전역 순서)를 유지한다.

![image](https://media.geeksforgeeks.org/wp-content/uploads/20230310184135/Sequential.png)

#### 인과적 일관성(Causal Consistency)

어떤 경우에는 인과적으로 관련된 작업이 올바른 순서로 표시되는 한, 각 클라이언트 프로그램에서 지정한 순서를 유지할 필요가 없다.
즉, 첫 번째 이벤트가 두 번째 이벤트의 결과에 영향을 미치는 경우 모든 노드는 이벤트 B보다 먼저 이벤트 A를 일관된 순서로 보게 된다.

![image](https://media.geeksforgeeks.org/wp-content/uploads/20240220171849/Causal-consistency-copy.webp)

#### 최종 일관성(Eventual Consistency)

업데이트가 없는채로 일정 시간이 지나면 결국 모든 데이터 복제본이 결국 일관된 상태로 수렴한다는 일관성 모델이다.

![image](https://media.geeksforgeeks.org/wp-content/uploads/20240221105432/Eventual-Consistency-in-Distributive-Systems-2.webp)

### CAP Theorem's Consistency Model

CAP 정리는 이러한 여러 일관성 모델들을 두 가지 주요 범주로 구분한다.

- 강력한 일관성 모델(Strong consistency models)
  - 강력한 일관성 모델은 CAP 정리의 "C"에 해당하며 네트워크 분할 중에 사용 가능 해야 하는 시스템에서는 지원될 수 없다.
- 약한 일관성 모델(Weak consistency models)
  - 약한 일관성 모델은 네트워크 파티션 중에 가용성을 유지하면서 지원할 수 있는 모델이다.

현재 널리 사용되는 여러 분산 시스템(예: Apache Cassandra, DynamoDB 등)에서 제공하는 일관성 고려하면 일반적으로 두 가지 모델이 지원된다.

- 강력한 일관성
  - 선형성
- 약한 일관성
  - 최종 일관성

---

참고

- [Distributed Systems for Practitioners](https://www.educative.io/courses/distributed-systems-practitioners)
