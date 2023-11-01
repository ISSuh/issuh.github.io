---
title: Spring Data JPA - 공통 인터페이스 기능
date: 2023-10-30 17:00:00 +0900
categories: [Backend, JPA]
published: true
tags:
- Web
- Spring Framework
- DataBase
- JPA
- Spring Data JPA
---

### 공통 인터페이스 구성
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface ItemRepository extends JpaRepository<Item, Long> {
}
```

![Alt text](/assets/posts/img/spring/spring_data_jpa/spring_data_jpa_01_01.png)
  - 스프링 데이터 JPA가 구현 클래스 대신 생성
    -  org.springframework.data.repository.Repository를 구현한 클래스는 스캔 대상
      - memberRepository.getClass()  class com.sun.proxy.$ProxyXXX
    - @Repository 어노테이션 생략 가능
      - 컴포넌트 스캔을 스프링 데이터 JPA가 대신 처리
      - JPA 예외를 스프링 예외로 변환하는 과정도 자동으로 처리


#### 공통인터페이스 분석
```java
// JpaRepository 공통 기능 인터페이스
public interface JpaRepository<T, ID extends Serializable> 
                                     extends PagingAndSortingRepository<T, ID> 
{
    ...
}
```
```java
// JpaRepository를 사용하는 인터페이스
import org.springframework.data.jpa.repository.JpaRepository;

public interface ItemRepository extends JpaRepository<Item, Long> {
}
```
  - JpaRepository 인터페이스
    - 공통 CRUD 제공
  - 제네릭은 <엔티티 타입, 식별자 타입> 설정

![Alt text](/assets/posts/img/spring/spring_data_jpa/spring_data_jpa_01_02.png)
  - 제네릭 타입
    - T
      - 엔티티
    - ID
      - 엔티티의 식별자 타입
    - S
      - 엔티티와 그 자식 타입
  - 주요 메소드
    - save(S)
      - 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합
    - delete(T)
      - 엔티티 하나를 삭제
      - 내부에서 EntityManger.remove() 호출
    - findById(ID)
      - 엔티티 하나를 조회
      - 내부에서 EntityManger.find() 호출
    - getOne(ID)
      - 엔티티를 프록시로 조회
      - 내부에서 EntityManger.getReference() 호출
    - findAll(...)
      - 모든 엔티티를 조회
      - 정렬(Sort)이나 페이징(Pageable) 조건을 파라미터로 제공 가능

> JpaRepository는 대부분의 공통 메소드를 제공 함.

---
참고 
 - 실전! 스프링 데이터 JPA(김영한)
