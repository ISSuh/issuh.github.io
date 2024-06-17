---
title: Distributed Systems - Coordination Patterns\
date: 2024-06-17 22:31:00 +0900
categories: [System Design, Distributed Systems]
published: true
tags:
  - System Design
  - Distributed Systems
---

### Orchestration

오케스트레이션은 단일 중앙 시스템이 다양한 다른 시스템의 실행을 조정하는 역할을 담당하므로 스타 토폴로지와 유사한 모습을 보인다.
이때 중앙 시스템을 오케스트레이터 라고 부른다.

![image](https://blog.kakaocdn.net/dn/ypCWe/btsBl56WrhM/vCCMlFLcXkesOgFdWxul80/img.png)

### Choreography

Choreography 에서 시스템은 외부 코디네이터가 없어도 서로 조정된다.
이들은 본질적으로 체인에 배치되며 각 시스템은 토폴로지의 이전 시스템과 다음 시스템을 인식한다.
요청은 체인을 통해 각 시스템에서 다음 시스템으로 연속적으로 전달된다.

![image](https://alok-mishra.com/wp-content/uploads/2022/07/choreography-alok-mishra.jpeg)

### The behavior of coordination patterns

![image](https://alok-mishra.com/wp-content/uploads/2022/07/orchestration-or-choreography.png?w=640)

Orchestration과 Choreography 패턴 모두 원자성을 보장해야 한다.

원자성을 달성하기 위해 아래와 같은 두가지 방법을 사용할 수 있다.

- 2단계 커밋
- 사가 트랜잭션

첫 번째 접근 방식은 오케스트레이터가 2단계 커밋 프로토콜의 조정자 역할을 하는 오케스트레이터 패턴에 더 잘 맞는 반면, 두 번째 접근 방식은 두 패턴 모두에서 사용할 수 있다.

---

참고

- [Distributed Systems for Practitioners](https://www.educative.io/courses/distributed-systems-practitioners)
