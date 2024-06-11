---
title: Distributed Systems - Partitioning
date: 2024-06-11 22:02:00 +0900
categories: [System Design, Distributed Systems]
published: true
tags:
  - System Design
  - Distributed Systems
---

### Partitioning

분산 시스템의 주요 이점중 하나는 **확장성(Scalability)** 이다.
이러한 확장성을 달성하기 위한 주요 구현 방식중 하나는 파티셔닝(Partitioning) 이다.

파티셔닝은 데이터 세트를 여러개의 작은 데이터 세트로 분할한 다음 이를 저장하고 처리하는 책임을 분산 시스템의 여러 노드에 각각 할당하는 방법을 말한다.

파티셔닝에는 크게 두가지 변형이 존재한다.

- **Vertical partitioning(수직 파티셔닝)**
- **Horizontal partitioning(수평 파티셔닝)**
  - **샤딩**

![image](https://miro.medium.com/v2/resize:fit:800/1*fSQex1AygqpyDQpWooK38A.png)

#### Vertical partitioning

수직 파티셔닝은 테이블을 더 적은 수의 열이 포함된 여러 테이블로 나누고 나뉘어진 테이블을 서로 연결하기 위해 추가적인 행을 별도로 추가한다.
나뉜 두 테이블을 연결하는 작업을 일반적으로 **조인**작업이라고 부른다.

**정규화**를 통해 수직 파티셔닝을 수행할 수 있다.

이러한 수직 파티셔닝 시스템에선 서로다른 테이블의 데이터를 결합하는 요청에 대해 효율이 떨어지게 된다.

#### Horizontal partitioning

수평 파티셔닝은 테이블을 같은 행을 가진 여러개의 작은 테이블로 나누고 각 테이블에 일정한 수의 데이터가 반영되도록 하는 방법이다.

![image](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRjPiO3RI_2KeT7ZzikadM6nyhywwyZJi1IUg&s)

수평 파티셔닝은 서로 다른 노드에 있는 데이터에 대해 데이터를 수평으로 분할할 때 원자성 작업을 수행하는 것이 훨씬 더 어렵다.

### Algorithms for Horizontal Partitioning

수평 분할을 구현하기 위한 여러 알고리즘이 존재한다.

![image](https://docs.oracle.com/en/database/oracle/oracle-database/19/vldbg/img/vldbg005.gif)

#### Range partitioning

Range partitioning은 특정 속성의 값에 따라 데이터 세트를 범위로 분할하는 방법이다.

![image](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT5NbMTPnHADLzoHLEhk7Z1SsTrxzIgBewWQg&s)

- 장점
  - 구현의 용이성
  - 분할 키 값을 사용하여 범위 쿼리 수행 가능
  - 쿼리된 범위가 작고 단일 노드에 있는 경우 분할 키를 사용하는 범위 쿼리에 대한 성능이 좋음
  - 범위 조정(재 분할)이 더 쉽고 효율적임
- 단점
  - 분할 키 이외의 키를 사용하여 범위 쿼리를 수행할 수 없음
  - 쿼리된 범위가 크고 여러 노드에 상주하는 경우 분할 키를 사용하는 범위 쿼리의 성능이 저하됨
  - 트래픽이나 데이터의 고르지 못한 분포로 인해 일부 노드에 과부하가 발생할 수 있음

#### Hash partitioning

Hash partitioning은 각 행의 특정 속성에 해시 함수를 적용하여 해당 행이 속하는 파티션을 결정하는 방법이다.

![image](https://www.thedigitalcatonline.com/images/data-partitioning-and-consistent-hashing/hash_functions.jpg)

- 장점
  - 키를 해시 함수를 통해 만드므로 런타임시 상대적으로 쉽게 분할 매핑을 계산 할 있음
  - 해시 함수가 시스템 노드 전체에 데이터를 균일하게 배포하여 일부 노드의 과부하를 방지할 수 있음
- 단점
  - 추가 데이터를 저장하거나 모든 노드를 쿼리하지 않으면 분할 키로 사용하는 속성에 대해서도 범위 쿼리를 수행할 수 없음
  - 시스템에서 노드를 추가하거나 제거하면 다시 분할해야 하며 이로인해 시스템의 모든 노드에서 상당한 데이터 이동이 발생할 수 있음

#### Consistent hashing

Consistent hashing은 Hash partitioning과 매우 유사한 분할 기술 이지만 해시 분할로 인해 발생하는 데이터 이동 문제를 해결한다.

시스템은 링 이라고 불리우는 **[0, L]** 범위 값이 생성한다.(예: [0, 360]).
각 노드는 링의 범위내 특정 값 **s**를 할당 받는다.
그런 다음 시스템은 속성 값이 있는 레코드를 분할 키로 사용하여 **hash(s) mod L** 계산을 통해 링의 해당 지점 뒤에 있는 노드를 찾습니다.

새로운 노드가 링에 진입하면 링의 다음 노드에서만 데이터를 받는다.
이외 노드는 더이상 다른 데이터를 교환할 필요가 없다.

![image](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTkFJ2J4ViDtOeAtAYdXdex6oaJsz_aIhaYAA&s)

- 장점
  - 시스템에 노드를 추가하거나 제거할때 데이터 이동을 감소할 수 있음
- 단점
  - 링에 있는 노드의 무작위 할당으로 인해 데이터가 균일하지 않게 분포될 수 있음
  - 노드가 추가되거나 제거됨에 따라 데이터 분포가 더 불균형해질 수 있음

---

참고

- [Distributed Systems for Practitioners](https://www.educative.io/courses/distributed-systems-practitioners)
