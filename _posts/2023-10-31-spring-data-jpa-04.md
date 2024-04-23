---
title: Spring Data JPA - 스프링 데이터 JPA 구현체 분석
date: 2023-10-31 16:10:00 +0900
categories: [Backend, JPA]
published: true
tags:
  - Web
  - Spring Framework
  - DataBase
  - JPA
  - Spring Data JPA
---

### 스프링 데이터 JPA 구현체 분석

- 스프링 데이터 JPA가 제공하는 공통 인터페이스의 구현체
  - org.springframework.data.jpa.repository.support.SimpleJpaRepository

```java
@Repository
@Transactional(readOnly = true)
public class SimpleJpaRepository<T, ID> ...{

  @Transactional
  public <S extends T> S save(S entity) {
    if (entityInformation.isNew(entity)) {
      em.persist(entity);
      return entity;
    } else {
      return em.merge(entity);
    }
  }
    ...
}
```

- @Repository 적용
  - JPA 예외를 스프링이 추상화한 예외로 변환
- @Transactional 적용
  - JPA의 모든 변경은 트랜잭션 안에서 동작
  - 스프링 데이터 JPA는 변경(등록, 수정, 삭제) 메소드를 트랜잭션 처리
  - 서비스 계층에서 트랜잭션을 시작하지 않으면 리포지토리에서 트랜잭션 시작
  - 서비스 계층에서 트랜잭션을 시작하면 리포지토리는 해당 트랜잭션을 전파 받아서 사용
- 클래스에 @Transactional(readOnly = true) 적용
  - 데이터를 단순히 조회만 하고 변경하지 않는 트랜잭션에서 readOnly = true 옵션을 사용하면,
    내부적으로 flush를 생략하기 떄문에 약간의 성능향상을 얻을 수 있음
- **save 메소드**
  - **새로운 엔티티면 저장(persist())**
  - **새로운 엔티티가 아니면 병합(merge())**
    - 병합은 엔티티의 값을 강제로 엎어 적용하는 것이기 떄문에 의도치 않게 값이 null이 될수도 있음

### 새로운 엔티티를 구별하는 방법

- 새로운 엔티티를 판단하는 기본 전략
  - 식별자가 객체일떄는 null 인지 판단
  - 식별자가 자바 기본 타입일때는 0으로 판단
  - Persistable 인터페이스를 구현해서 판단 로직 변경 가능

```java
package org.springframework.data.domain;
public interface Persistable<ID> {
    ID getId();
    boolean isNew();
}
```

JPA 식별자 생성 전략이 @GenerateValue면 save() 호출 시점에 식별자가 없으므로 새로운 엔티티로 인식해서 정상 독작 함.
그러나 JPA 식별자 생성 전략이 @Id 만 사용하여 직접 할당하는 경우엔 이미 식별자 값이 있는 상태로 save()를 호출하므로,
이 경우에는 merge()가 호출 됨.

merge()는 우선 데이터베이스를 호출하여 값을 확인하고, 데이터베이스에 값이 없다면 새로운 엔티티로 인지하므로 비효율적임.
따라서 Persistable을 사용하여 새로운 엔티티 확인 여부를 직접 구현하는 것이 효과적 임.
예를 들어 등록시간(@CreateDate)을 조합하여 사용하면 이 필드로 새로운 엔티티 여부를 편리하게 확인할 수 있음.
(@CreateDate에 값이 없으면 새로운 엔티티로 판단)

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Item implements Persistable<String> {
  @Id
  private String id;

  @CreatedDate
  private LocalDateTime createdDate;

  public Item(String id) {
    this.id = id;
  }

  @Override
  public String getId() {
    return id;
  }

  @Override
  public boolean isNew() {
    return createdDate == null;
  }
}
```

---

참고

- 실전! 스프링 데이터 JPA(김영한)
