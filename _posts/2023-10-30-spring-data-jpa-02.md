---
title: Spring Data JPA - 쿼리 메소드 기능
date: 2023-10-30 17:50:00 +0900
categories: [Backend, JPA]
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

### @Query. 리포지토리 메소드에 쿼리 정의
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  @Query("select m from Member m where m.username= :username and m.age = :age")
  List<Member> findUser(@Param("username") String username, @Param("age") int age);
}
```
  - @org.springframework.data.jpa.repository.Query 어노테이션 사용
  - 실행할 메소드에 정적 쿼리를 직접 작성하므로 이름없는 NamedQuery라고 할 수 있음
  - JPA NamedQuery처럼 어플리케이션 실행 시점에 문법 오류를 발견 할 수 있음

> 보통 실무에서는 메소드 이름으로 쿼리 생성 기능은 파라미터가 증가하면
> 메소드 이름이 매우 길어지므로  @Query 기능을 자주 사용함

#### @Query로 값, DTO 조회
```java
@Data
public class MemberDto {
  private Long id;
  private String username;
  private String teamName;
 
 public MemberDto(Long id, String username, String teamName) {
    this.id = id;
    this.username = username;
    this.teamName = teamName;
  }
}
```
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  // DTO로 직접 조회
 @Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) " +
        "from Member m join m.team t")
  List<MemberDto> findMemberDto();
  
  // 단순히 값 하나를 조회
  @Query("select m.username from Member m")
  List<String> findUsernameList();
}
```
  - DTO로 직접 조회하려면 JPA의 new 명령어를 사용해야함
  - DTO에는 생성자를 정의해주고 그 생성자에 맞춰 필드를 넣어주어야 함

#### 파라미터 바인딩
```sql
-- 위치 기반
select m from Member m where m.username = ?0

-- 이름 기반
select m from Member m where m.username = :name
```

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  @Query("select m from Member m where m.username = :name")
  Member findMembers(@Param("name") String username); 

  @Query("select m from Member m where m.username in :names")
  List<Member> findByNames(@Param("names") List<String> names);
}
```
  - 보통 코드의 가독성과 유지보수를 위해 이름기반 파라미터 바인딩을 사용
  - Collection 타입으로 IN 정도 지원 함

### 반환 타입
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  // 컬렉션 반환
  List<Member> findByUsername(String name);

  // 단건 반환
  Member findByUsername(String name);

  // 단건 Optional 반환
  Optional<Member> findByUsername(String name); 
}
```
  - [스프링 데이터 JPA 공식 문서 참고](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repository-query-return-types)
  - 조회 결과가 많거나 없으면 아래와 같이 처리
    - 컬렉션
      - 결과 없음 : 빈 컬렉션 반환
    - 단건 조회
      - 결과 없음 : null 반환
      - 결과가 2건 이상 : javax.persistence.NonUniqueResultException 예외 발생
  
### 페이징과 정렬
  - 페이징 및 정렬 기능
    - org.springframework.data.domain.Sort
      - 정렬 기능
    - org.springframework.data.domain.Pageable
      - 페이징 기능
      - 내부에 Sort 포함
  - 반환 타입
    - org.springframework.data.domain.Page
      - 추가 count 쿼리 결과를 포함하는 페이징
    - org.springframework.data.domain.Slice
      - 추가 count 쿼리 없이 다음 페이지만 학인 가능
      - 내부적으로 limit + 1 조회
    - List
      - 추가 count 쿼리 없이 결과만 반환

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  // count 쿼리 사용
  Page<Member> findByAge(int age, Pageable pageable);

  // count 쿼리 사용 안함
  Slice<Member> findByAge(int age, Pageable pageable);

  // count 쿼리 사용 안함
  List<Member> findByAge(int age, Pageable pageable);

  List<Member> findByAge(int age, Sort sort);
}
```
```java
@Test
public void paging() throws Exception {
  //given
  memberRepository.save(new Member("member1", 10));
  memberRepository.save(new Member("member2", 10));
  memberRepository.save(new Member("member3", 10));
  memberRepository.save(new Member("member4", 10));
  memberRepository.save(new Member("member5", 10));
  int age = 10;
  int offset = 0;
  int limit = 3;

  //when
  PageRequest pageRequest = PageRequest.of(offset, limit, Sort.by(Sort.Direction.DESC, "username"));
  Page<Member> page = memberRepository.findByAge(age, pageRequest);
  
  //then
  List<Member> content = page.getContent();
  Assertions.assertThat(content.size()).isEqualTo(3); //조회된 데이터 수 
  Assertions.assertThat(page.getTotalElements()).isEqualTo(5); //전체 데이터 수
  Assertions.assertThat(page.getNumber()).isEqualTo(0); //페이지 번호
  Assertions.assertThat(page.getTotalPages()).isEqualTo(2); //전체 페이지 번호
  Assertions.assertThat(page.isFirst()).isTrue(); //첫번째 항목인가?
  Assertions.assertThat(page.hasNext()).isTrue(); //다음 페이지가 있는가?
}
```

<details>
<summary>생성되는 SQL 로그</summary>
<div markdown="1">

```log
2023-10-31 10:44:00.902 DEBUG 36924 --- [           main] org.hibernate.SQL                        : 
    select
        member0_.member_id as member_i1_0_,
        member0_.age as age2_0_,
        member0_.team_id as team_id4_0_,
        member0_.username as username3_0_ 
    from
        member member0_ 
    where
        member0_.age=? 
    order by
        member0_.username desc limit ?
...

2023-10-31 10:44:00.911 DEBUG 36924 --- [           main] org.hibernate.SQL                        : 
    select
        count(member0_.member_id) as col_0_0_ 
    from
        member member0_ 
    where
        member0_.age=?
```

</div>
</details>
  - 두번쨰 파라미터로 받은 Pageable은 인터페이스 이므로 실제 사용할 떄는 해당 인터페이스를 구현한
  org.springframework.data.domain.PageRequest 객체를 사용
  - PageRequest 생성자의 첫번쨰 파라미터는 현재 페이지를, 두번쨰 파라미터는 조회할 데이터의 수를 입력.
  그리고 추가로 정렬 정보도 파라미터로 사용 가능
  - 페이지는 0부터터 시작 함

#### 페이지를 유지하면서 엔티티를 DTO로 변환
엔티티를 바로 클라이언트에게 전달하는것은 피하는 것이 좋으므로, 엔티티를 별도의 DTO로 재구성할 필요가 있음

```java
@Test
public void paging() throws Exception {

  ...

  //when
  PageRequest pageRequest = PageRequest.of(offset, limit, Sort.by(Sort.Direction.DESC, "username"));
  Page<Member> page = memberRepository.findByAge(age, pageRequest);
  
  Page<MemberDto> dtoPage = page.map(m -> new MemberDto(m.getId(), m.getUsername()));

  ...
}
```

#### count 쿼리 분리
다음과 같이 count 쿼리를 별도로 지정하여 살행할 수 있음

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  @Query(value = "select m from Member m",
        countQuery = "select count(m.username) from Member m")
  Page<Member> findMemberAllCountBy(Pageable pageable);
}
```

<details>
<summary>생성되는 SQL 로그</summary>
<div markdown="1">


```log
2023-10-31 10:57:56.716 DEBUG 37534 --- [           main] org.hibernate.SQL                        : 
    select
        member0_.member_id as member_i1_0_,
        member0_.age as age2_0_,
        member0_.team_id as team_id4_0_,
        member0_.username as username3_0_ 
    from
        member member0_ 
    order by
        member0_.username desc limit ?
...

2023-10-31 10:57:56.722 DEBUG 37534 --- [           main] org.hibernate.SQL                        : 
    select
        count(member0_.username) as col_0_0_ 
    from
        member member0_
...
```

</div>
</details>
  
  - 데이터베이스의 전체 row의 수를 반한하는 count 쿼리는 매우 무겁기 떄문에 최적화 필요.
  - 복잡한 SQL에서는 데이터를 Left Join으로 조회할때,
  자동으로 생성되는 count 쿼리도 동일하게 Left Join을 사용하여 전체 수를 조회함.
  그러나 단순 count 쿼리에서 까지 Left Join할 필요가 없으므로 최적화를 위해 별도의 count 쿼리를 지정 필요.

> 스프링부트3 - 하이버네이트6에서는 의미없는 Left Join을 최적화 하여
> 위 예시의 경우 Left Join을 추가하지 않음

### 벌크성 수정 쿼리
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  @Modifying(clearAutomatically = true)
  @Query("update Member m set m.age = m.age + 1 where m.age >= :age")
  int bulkAgePlus(@Param("age") int age);
}
```
  - 벌크성 수정, 삭제 쿼리는 @Modifying 어노테이션 사용
    - 사용하지 않으면 다음과 같은 예외 발생
    - org.hibernate.hql.internal.QueryExecutionRequestException: Not supported for DML operations
  - 벌크성 쿼리를 실행하고 나서 영속성 컨텍스트 초기화
    - @Modifying(clearAutomatically = true)
    - 이 옵션 없다면 영속성 컨텍스트에 과거 값이 남아서 재조회시 문제가 될수 있음
  - 벌크 연산은 영속성 컨텍스트를 무시하고 실행되기 떄문에 영속성 컨텍스트에 있는 엔티티 상태와 데이터베이스에 엔티티 상태가 달라질 수 있음
    - 권장하는 방안으로는 아래와 같이 두가지가 있음
      - 영속성 컨텍스트에 엔티티가 없는 상태에서 벌크 연산을 먼저 실행
      - 부득이하게 영속성 컨텍스트에 엔티티가 있다면 벌크 연산 직후 영속성 컨텍스트를 초기화
        - entityManager.clear()

### @EntityGraph
관련된 엔티티들을 SQL 한번에 조회하는 방법

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  // 공통 메소드 오버라이드
  @Override
  @EntityGraph(attributePaths = {"team"})
  List<Member> findAll();

  // 메소드 이름으로 쿼리 + 엔티티 그래프
  @EntityGraph(attributePaths = {"team"})
  List<Member> findByUsername(String username);

  // JPQL + 엔티티 그래프
  @EntityGraph(attributePaths = {"team"})
  @Query("select m from Member m")
  List<Member> findMemberEntityGraph();
```
  - 사실상 패치조인의 간편버전
  - 내부적으로 LEFT OUTER JOIN 사용

### JPA Hint
JPA 쿼리 힌트(SQL 힌트가 아니라 JPA 구현체에게 제공하는 힌트)

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  // read only 로 힌트 적용
  @QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
  Member findReadOnlyByUsername(String username);

  // read only 로 힌트 적용
  @QueryHints(value = { @QueryHint(name = "org.hibernate.readOnly", value = "true")},
              forCounting = true)
  Page<Member> findByUsername(String username, Pageable pageable);
}
```
```java
@Test
public void queryHint() throws Exception {
  //given
  memberRepository.save(new Member("member1", 10));
  em.flush();
  em.clear();

  //when
  Member member = memberRepository.findReadOnlyByUsername("member1");
  member.setUsername("member2");

  // 쿼리 힌트로 인하여 read only로 설정되었으므로
  // Update Query를 실행하지 않음
  em.flush(); 
}
```
  - org.springframework.data.jpa.repository.QueryHints 어노테이션 사용
  - forCounting
    - 반환 타입으로 Page 인터페이스를 적용하면 추가로 호출하는 페이징을 위한 count 쿼리도 쿼리 힌트 적용
    - 기본값 true

### Lock
연관된 엔티티에 대하여 Lock을 걸어 다른 세션에서 접근하지 못하도록 함

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  @Lock(LockModeType.PESSIMISTIC_WRITE)
  List<Member> findLockByUsername(String username);
}
```

<details>
<summary>생성되는 SQL 로그</summary>
<div markdown="1">

```log
2023-10-31 12:29:08.176 DEBUG 40588 --- [           main] org.hibernate.SQL                        : 
    select
        member0_.member_id as member_i1_0_,
        member0_.age as age2_0_,
        member0_.team_id as team_id4_0_,
        member0_.username as username3_0_ 
    from
        member member0_ 
    where
        member0_.username=? for update
```

</div>
</details>

  - org.springframework.data.jpa.repository.Lock 어노테이션을 사용
  - 내부적으로 SQL의 for update와 같은 구문 사용
  - 어플리케이션이 실시간이 중요하다면 사용에 주의 필요

---
참고 
 - 실전! 스프링 데이터 JPA(김영한)
