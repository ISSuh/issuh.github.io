---
title: Spring Data JPA - 쿼리 메소드 기능
date: 2023-10-30 17:50:00 +0900
categories: [Web, Spring Framework]
published: true
tags:
- Web
- Spring Framework
- DataBase
- JPA
- Spring Data JPA
---

### 메소드 이름으로 쿼리 생성
메소드 이름을 분석해서 JPQL 쿼리 실행

```java
public interface MemberRepository extends JpaRepository<Member, Long> {

  // 이름이 username과 같고 나이가 age 보다 많은 Member의 리스트 반환
  List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
}
```
  - 쿼리 메소드 필터 조건
    - [스프링 데이터 JPA 공식 문서 참고](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)

#### 스프링 데이터 JPA가 제공하는 쿼리 메소드 기능
  - 조회: find...By ,read...By ,query...By  get...By, 
    - [Query Creation 문서 참고](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-creation)
    - findHelloBy 처럼 ...에 식별하기 위한 내용(설명)이 들어가도 됨
  - COUNT: count...By
    - 반환타입 long
  - EXISTS: exists...By
    - 반환타입 boolean
  - 삭제: delete...By, remove...By
    - 반환타입 long
  - DISTINCT: findDistinct, findMemberDistinctBy
  - LIMIT: findFirst3, findFirst, findTop, findTop3
    - [Limiting Query Results 문서 참고](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.limit-query-result)

<br>

이 기능은 엔티티의 필드명이 변경되면 인터페이스에 정의한 메소드 이름도 꼭 함꼐 변경해야 함.
그렇지 않으면 어플리케이션 실행 시점에 오류 발생.

### JPA NamedQuery
JPA의 NamedQuery를 호출할 수 있음

```java
// Entity에 NameQuery 정의
@Entity
@NamedQuery(
  name="Member.findByUsername",
  query="select m from Member m where m.username = :username")
public class Member {
    ...
}
```
```java
// JPA를 직접 사용하여 NamedQuery 호출
public class MemberRepository {
    public List<Member> findByUsername(String username) {
        ...
        List<Member> resultList = 
            em.createNamedQuery("Member.findByUsername", Member.class)
                .setParameter("username", username)
                .getResultList();
    }
}
```
```java
public interface MemberRepository extends JpaRepository<Member, Long> {

  // 스프링 데이터 JPA로 NamedQuery 호출
  @Query(name = "Member.findByUsername")
  List<Member> findByUsername(@Param("username") String username);

  ...
}
```
  - 스프링 데이터 JPA는 선언한 "도메인 클래스 + . + 메소드 이름" 으로 NamedQuery를 찾아서 실행
  - 만약 실행할 NamedQuery가 없다면 메소드 이름으로 쿼리 생성 전략을 사용
  - 어플리케이션 로딩시점에 정의한 NamedQuery를 파싱하여 검사함
    - 문법오류가 있다면 로딩 시점에 예외 발생
  - 스프링 데이터 JPA를 사용하면 실무에서 NamedQuery를 직접 등록하여 사용할 일은 거의 없음
    - NamedQuery대신 @Query를 사용하여 리포지토리 메소드에 쿼리를 직접 정의

---
참고 
 - 실전! 스프링 데이터 JPA(김영한)
