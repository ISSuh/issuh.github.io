---
title: QueryDSL - 기본 문법
date: 2023-11-01 10:40:00 +0900
categories: [Backend, QueryDSL]
published: true
tags:
  - Web
  - Spring Framework
  - DataBase
  - JPA
  - QueryDSL
---

### 기본 QTypq 활용

```java
//별칭 직접 지정
QMember qMember = new QMember("m");

//기본 인스턴스 사용
QMember qMember = QMember.member;
```

```java
//기본 인스턴스를 static import와 함께 사용

import static study.querydsl.entity.QMember.*;

@Test
public void startQuerydsl3() {
  Member findMember =
    queryFactory
      .select(member)
      .from(member)
      .where(member.username.eq("member1"))
      .fetchOne();
  ...
}
```

- 같은 테이블을 조인하는 경우가 아니면 되도록이면 기본 인스턴스를 사용

### 검색 조건 쿼리

```java
@Test
public void search() {
  Member findMember =
    queryFactory
      .selectFrom(QMember.member)
      .where(QMember.member.username.eq("member1")
        .and(QMember.member.age.eq(10)))
      .fetchOne();

  ...
}
```

- 검색 조건은 .and(), .or()를 메소드 체인으로 연결 가능

JPQL이 제공하는 모든 검색조건들을 제공 함.

```java
member.username.eq("member1") // username = 'member1'
member.username.ne("member1") //username != 'member1'
member.username.eq("member1").not() // username != 'member1'

member.username.isNotNull() //이름이 is not null

member.age.in(10, 20) // age in (10,20)
member.age.notIn(10, 20) // age not in (10, 20)
member.age.between(10,30) //between 10, 30

member.age.goe(30) // age >= 30
member.age.gt(30) // age > 30
member.age.loe(30) // age <= 30
member.age.lt(30) // age < 30

member.username.like("member%") //like 검색
member.username.contains("member") // like ‘%member%’ 검색
member.username.startsWith("member") //like ‘member%’ 검색
...
```

### 결과 조회

```java
//List
List<Member> fetch = queryFactory
        .selectFrom(member)
        .fetch();

//단 건
Member findMember1 = queryFactory
        .selectFrom(member)
        .fetchOne();

//처음 한 건 조회
Member findMember2 = queryFactory
        .selectFrom(member)
        .fetchFirst();

//페이징에서 사용
QueryResults<Member> results = queryFactory
        .selectFrom(member)
        .fetchResults();

//count 쿼리로 변경
long count = queryFactory
        .selectFrom(member)
        .fetchCount();
```

- fetch()
  - 리스트 조회
  - 데이터가 없으면 빈 리스트 반환
- fetchOne()
  - 단건 조회
  - 결과가 없으면 null
  - 결과가 둘 이상이면 com.querydsl.core.NonUniqueResultException
- fetchFirst()
  - limit(1).fetchOne() 과 동일
- fetchResuls()
  - 페이징 정보 포함
  - total count 쿼리가 추가 실행
- fetchCount()
  - count 쿼리로 변경해서 count 수 조회

### 정렬

```java
List<Member> result =
  queryFactory
    .selectFrom(QMember.member)
    .where(QMember.member.age.eq(100))
    .orderBy(QMember.member.age.desc(), QMember.member.username.asc().nullsLast())
    .fetch();
```

- desc(), asc()
  - 일반 정렬
- nullLast(), nullFirst()
  - null 데이터 순서 부여

### 페이징

```java
// 조회 건수 제한
List<Member> result =
  queryFactory
    .selectFrom(QMember.member)
    .orderBy(QMember.member.username.desc())
    .offset(1)  // 0부터 시작(zero index)
    .limit(2)    // 최대 2건 조회
    .fetch();

// 전체 조회
QueryResults<Member> result =
  queryFactory
    .selectFrom(QMember.member)
    .orderBy(QMember.member.username.desc())
    .offset(1)  // 0부터 시작(zero index)
    .limit(2)    // 최대 2건 조회
    .fetchResults();
```

- count 쿼리가 실행되어 성능상 주의가 필요
  - 실무에서 페이징 쿼리를 작성할 떄, 데이터를 조회하는 쿼리는 여러 테이블을 조회해야 하지만,
    count 쿼리는 조인이 필요없는 경우도 있음. 그러나 자동화된 count 쿼리는
    원본 쿼리와 같이 모두 조인을 하기 떄문에 성능이 안나올 수 있음.
  - count 쿼리에 조인이 필요없는 성능 최적화가 필요하다면 count 전용 쿼리를 별도로 작성해야 함.

### 집합

```java
/**
 * JPQL
 * select
 *    COUNT(m),   //회원수
 *    SUM(m.age), //나이 합
 *    AVG(m.age), //평균 나이
 *    MAX(m.age), //최대 나이
 *    MIN(m.age)  //최소 나이
 * from Member m
 */
@Test
public void aggregation() throws Exception {
  List<Tuple> result = queryFactory
    .select(QMember.member.count(),
            QMember.member.age.sum(),
            QMember.member.age.avg(),
            QMember.member.age.max(),
            QMember.member.age.min())
    .from(QMember.member)
    .fetch();

  Tuple tuple = result.get(0);
  Assertions.assertThat(tuple.get(QMember.member.count())).isEqualTo(4);
  Assertions.assertThat(tuple.get(QMember.member.age.sum())).isEqualTo(100);
  Assertions.assertThat(tuple.get(QMember.member.age.avg())).isEqualTo(25);
  Assertions.assertThat(tuple.get(QMember.member.age.max())).isEqualTo(40);
  Assertions.assertThat(tuple.get(QMember.member.age.min())).isEqualTo(10);
}
```

- JPQL이 제공하는 모든 집합 함수 제공
- Querydsl의 Tuple 타입으로 반환

GroupBy 사용

```java
/**
 * 팀의 이름과 각 팀의 평균 연령을 구해라.
 */
@Test
public void group() {
  List<Tuple> result = queryFactory
    .select(QTeam.team.name, QMember.member.age.avg())
    .from(QMember.member)
    .join(QMember.member.team, QTeam.team)
    .groupBy(QTeam.team.name)
    .fetch();

  Tuple teamA = result.get(0);
  Tuple teamB = result.get(1);

  Assertions.assertThat(teamA.get(QTeam.team.name)).isEqualTo("teamA");
  Assertions.assertThat(teamA.get(QMember.member.age.avg())).isEqualTo(15);

  Assertions.assertThat(teamB.get(QTeam.team.name)).isEqualTo("teamB");
  Assertions.assertThat(teamB.get(QMember.member.age.avg())).isEqualTo(35);
}
```

- 그룹화된 결과를 제한하려면 having 사용

### 조인 - 기본 조인

조인의 기본 문법은 첫번쨰 파라미터에 조인 대상을 지정하고, 두번쨰 파라미터에 별칭으로 사용할 QType을 지정

```java
join(조인 대상, 별칭으로 사용할 Q타입)
```

```java
// 팀 A에 소속된 모든 회원
List<Member> result = queryFactory
  .selectFrom(QMember.member)
  .join(QMember.member.team, QTeam.team)
  .where(QTeam.team.name.eq("teamA"))
  .fetch();

//세타 조인(연관관계가 없는 필드로 조인)
//회원의 이름이 팀 이름과 같은 회원 조회
List<Member> result = queryFactory
  .select(QMember.member)
  .from(QMember.member, QTeam.team)
  .where(QMember.member.username.eq(QTeam.team.name))
  .fetch();
```

- join(), innerJoin()
  - 내부 조인(inner join)
- leftJoin()
  - left 외부 조인(left outer join)
- rightJoin()
  - right 외부 조인(right outer join)
- JPQL의 ON과 성능 최적화를 위한 fetch 조인 제공

### 조인 - on절

**조인 대상 필터링**

```java
/**
 * 예) 회원과 팀을 조인하면서, 팀 이름이 teamA인 팀만 조인, 회원은 모두 조회
 * JPQL: SELECT m, t FROM Member m LEFT JOIN m.team t on t.name = 'teamA'
 * SQL: SELECT m.*, t.* FROM Member m LEFT JOIN Team t ON m.TEAM_ID=t.id and t.name='teamA'
*/
List<Tuple> result = queryFactory
  .select(QMember.member, QTeam.team)
  .from(QMember.member)
  .leftJoin(QMember.member.team, QTeam.team)
    .on(QTeam.team.name.eq("teamA"))
  .fetch();
```

- on 절을 활용해 조인 대상을 필터링 할 떄, 외부조인이 아닌 내부조인을 사용하면,
  where 절에서 필터링 한 것과 동일 함.

**연관관계 없는 엔티티 외부 조인**

```java
/**
 * 2. 연관관계 없는 엔티티 외부 조인
 * 예) 회원의 이름과 팀의 이름이 같은 대상 외부 조인
 * JPQL: SELECT m, t FROM Member m LEFT JOIN Team t on m.username = t.name
 * SQL: SELECT m.*, t.* FROM  Member m LEFT JOIN Team t ON m.username = t.name
 */
List<Tuple> result = queryFactory
  .select(QMember.member, QTeam.team)
  .from(QMember.member)
  .leftJoin(QTeam.team)
    .on(QMember.member.username.eq(QTeam.team.name))
  .fetch();
```

- 하이버네이트 5.1 부터 ON을 사용하여 서로 관계없는 필드로 외부 조인하는 기능이 추가 됨
- leftJoin() 부분에 일반 조인과 다르게 헨티티 하나만 들어감. 문법에 주의 필요
  - 일반 조인 : leftJoin(QMember.member.team, QTeam.team)
  - on조인 : from(QMember.member).leftJoin(QTeam.team).on(...)

### 조인 - 페치 조인

페치조인은 SQL에서 제공하는 기능이 아닌 JPA에서 SQL 조인을 활용하여 연관된 엔티티를 SQL 한번에 조회하는 기능.
주로 최적화에 사용하는 방법.

```java
// 패치 조인 미적용
// 지연로딩으로 Member, Team SQL 쿼리 각각 실행
@Test
public void fetchJoinNo() throws Exception {
  em.flush();
  em.clear();

  Member findMember =
    queryFactory
      .selectFrom(QMember.member)
      .where(QMember.member.username.eq("member1"))
      .fetchOne();

  boolean loaded = emf.getPersistenceUnitUtil().isLoaded(findMember.getTeam());
  Assertions.assertThat(loaded).as("페치 조인 미적용").isFalse();
}

// 패치 조인 적용
// 즉시로딩으로 Member, Team SQL 쿼리 한번으로 조회
@Test
public void fetchJoinUse() throws Exception {
  em.flush();
  em.clear();

  Member result = queryFactory
    .selectFrom(QMember.member)
    .join(QMember.member.team, QTeam.team).fetchJoin()
    .where(QMember.member.username.eq("member1"))
    .fetchOne();

  boolean loaded = emf.getPersistenceUnitUtil().isLoaded(result.getTeam());
  Assertions.assertThat(loaded).as("페치 조인 적용").isTrue();
}
```

- join(), leftJoin() 등 조인 기능 뒤에 fetchJoin() 이라고 추가하면 됨

### 서브 쿼리

com.querydsl.jpa.JPAExpressions 사용

```java
// 나이가 평균 나이 이상인 회원
QMember memberSub = new QMember("memberSub");

List<Member> result = queryFactory
  .selectFrom(QMember.member)
  .where(QMember.member.age.eq(
    JPAExpressions
      .select(memberSub.age.max())
      .from(memberSub)
  ))
  .fetch();
```

```java
// 서브쿼리 여러 건 처리, in 사용
QMember memberSub = new QMember("membersub");

List<Member> result = queryFactory
  .selectFrom(QMember.member)
  .where(QMember.member.age.in(
    JPAExpressions
      .select(memberSub.age)
      .from(memberSub)
      .where(memberSub.age.gt(10))
  ))
  .fetch();
```

- select 절에도 사용 가능
- from 절에는 서브쿼리 사용 불가능
  - JPA JPQL 서브쿼리의 한계점으로 from 절의 서브쿼리는 지원하지 않기 떄문에 Querydsl도 지원하지 않음

from 절에 서브쿼리를 써야한다면 다음과 같이 서브쿼리대신 풀어서 사용해야 함.

- 서브쿼리를 join으로 변경하는 방법
- 어플리케이션에서 쿼리를 분리하여 실행하는 방법
- nativeSQL을 사용하는 방법

### Case 문

select, where, order by에서 case문 사용 가능

```java
// 간단한 조건
List<String> result = queryFactory
  .select(QMember.member.age
    .when(10).then("열살")
    .when(20).then("스무살")
    .otherwise("기타"))
  .from(QMember.member)
  .fetch();

// 복잡한 조건
List<String> result = queryFactory
  .select(new CaseBuilder()
    .when(QMember.member.age.between(0, 20)).then("0~20살")
    .when(QMember.member.age.between(21, 30)).then("21~30살")
    .otherwise("기타"))
  .from(QMember.member)
  .fetch();
```

### 상수, 문자 더하기

상수가 필요하면 Expressions.constant(xxx) 사용

```java
// 상수 사용
Tuple result = queryFactory
  .select(QMember.member.username, Expressions.constant("A"))
  .from(QMember.member)
  .fetchFirst();

// 문자 더하기 concat
String result = queryFactory
  .select(QMember.member.username.concat("_").concat(QMember.member.age.stringValue()))
  .from(QMember.member)
  .where(QMember.member.username.eq("member1"))
  .fetchOne();
```

- 문자가 아닌 다른 타입들은 stringValue()로 문자로 변환할 수 있음.
  - 이 방법은 Enum을 처리할때 자주 사용

---

참고

- 실전! Querydsl(김영한)
