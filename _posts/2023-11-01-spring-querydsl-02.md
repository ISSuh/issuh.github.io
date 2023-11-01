---
title: QueryDsl - 중급 문법
date: 2023-11-01 14:30:00 +0900
categories: [Web, Spring Framework, QueryDsl]
published: true
tags:
- Web
- Spring Framework
- DataBase
- JPA
- Querydsl
---

### 프로젝션과 결과 반환 - 기본 
  - 프로젝션 :  select 대상 지정

```java
List<Tuple> result = queryFactory
  .select(member.username, member.age)
  .from(member)
  .fetch();

for (Tuple tuple : result) {
  String username = tuple.get(member.username);
  Integer age = tuple.get(member.age);
  System.out.println("username=" + username);
  System.out.println("age=" + age);
}
```
  - 프로젝션 대상이 둘 시상일떄 com.querydsl.core.Tuple 타입 사용
    - 결국 querydsl의 타입이므로 리포지토리 밖의 서비스나 컨트롤러에서 이 Tuple을 사용하는건 좋지 않음

### 프로젝션과 결과 반환 - DTO 조회
JPA에서 DTO를 조회할 떄는 new 명령머 및 DTO의 패키지 이름까지 다 적고, 생성자 방식만 가능하다는 단점이 있음.
Querydsl에서는 조회 결과를 DTO로 변환할떄 아래와 같은 3가지 방식을 지원함.
  - 프로퍼티 접근
  - 필드 직접 접근
  - 생성자 사용

```java
@Data
public class MemberDto {
  private String username;
  private int age;
  
  public MemberDto() {
  }

  public MemberDto(String username, int age) {
    this.username = username;
    this.age = age;
  }  
}
```
```java
// setter를 이용한 프로퍼티 접근
List<MemberDto> result = queryFactory
  .select(Projections.bean(
    MemberDto.class, QMember.member.username, QMember.member.age))
  .from(QMember.member)
  .fetch();

// 필드 직접 접근
List<MemberDto> result = queryFactory
  .select(Projections.fields(
    MemberDto.class, QMember.member.username, QMember.member.age))
  .from(QMember.member)
  .fetch();

// 생성자 사용
List<MemberDto> result = queryFactory
  .select(Projections.constructor(
      MemberDto.class, QMember.member.username, QMember.member.age))
  .from(QMember.member)
  .fetch();
```

만약 다른 필드의 이름이 달라 별칭을 따로 사용해야 할경우 아래와 같이 사용 가능.

```java
@Data
public class UserDto {
  private String name;
  private int age;
    
  public UserDto() {
  }

  public UserDto(String name, int age) {
    this.name = name;
    this.age = age;
  }
}
```
```java
QMember memberSub = new QMember("memberSub");

// name 이라는 별칭 사용
// 서브쿼리를 age라는 별칭으로 사용
List<UserDto> result = queryFactory
  .select(Projections.fields(
    UserDto.class, QMember.member.username.as("name"),
    ExpressionUtils.as(
      JPAExpressions
        .select(memberSub.age.max())
        .from(memberSub)
    , "age")))
  .from(QMember.member)
  .fetch();
```
  - ExpressionUtils.as(source,alias)
    - 필드나, 서브 쿼리에 별칭 적용
  - username.as("memberName")
    - 필드에 별칭 적용

### 프로젝션과 결과 반환 - @QueryProjection 
생성자 + @QueryProjection 을 사용하는 방법

```java
@Data
public class MemberDto {
  private String username;
  private int age;
  
  public MemberDto() {
  }

  @QueryProjection
  public MemberDto(String username, int age) {
    this.username = username;
    this.age = age;
  }  
}
```
```java
List<MemberDto> result = queryFactory
  .select(new QMemberDro(QMember.member.username, QMember.member.age))
  .from(QMember.member)
  .fetch();
```
  - DTO의 생성자에 @QueryProjection 선언후, 재빌드하여 Q파일을 생성해야 함
  - 컴파일떄 타입 에러를 체크할 수 있으므로 안전한 방법
    - 기존 DTO 조회(setter, 필드, 생성자 세가지 방식)는 컴파일때 타입에러를 체크할 수 없고 런타임이에 에러 발생
  - DTO에 QueryDSL 어노테이션으로 인하여 DTO가 QueryDSL에 의존하게 되어 아키텍처면으로 선택하기 어려울 떄가 있음

### 동적 쿼리
동적 쿼리를 해결하기 위해 아래 두가지 방식이 존재
  - BooleanBuilder
  - Where 다중 파라미터

#### BooleanBuilder 사용 방식
```java
@Test
public void dynamic_query() throws Exception {
  String usernameParam = "member1";
  Integer ageParam = 10;

  List<Member> result = searchMember(usernameParam, ageParam);
}

private List<Member> searchMember(String username, Integer age) {
  BooleanBuilder builder = new BooleanBuilder();
  
  if (username != null) {
    builder.and(QMember.member.username.eq(username));
  }

  if (age != null) {
    builder.and(QMember.member.age.eq(age));
  }

  return queryFactory
          .selectFrom(QMember.member)
          .where(builder)
          .fetch();
}
```

#### Where 다중 파라미터 사용 방식
```java
@Test
public void dynamic_query() throws Exception {
  String usernameParam = "member1";
  Integer ageParam = 10;

  List<Member> result = searchMember(usernameParam, ageParam);
}

private List<Member> searchMember(String username, Integer age) {
  return queryFactory
          .selectFrom(QMember.member)
          .where(usernameEq(username), ageEq(age))
          .fetch();
}

private BooleanExpression usernameEq(String username) {
  return username != null ? QMember.member.username.eq(username) : null;
}

private BooleanExpression ageEq(Integer age) {
  return age != null ? QMember.member.age.eq(age) : null;
}
```
  - where 조건에 null 값은 무시됨
  - 메소드를 다른 쿼리에서도 재활용 가능
  - 쿼리 자체의 가독성이 높아짐

아래와 같이 조합도 가능 함.

```java
private BooleanExpression allEq(String usernameCond, Integer ageCond) {
  return usernameEq(usernameCond).and(ageEq(ageCond));
}
```
  - null 체크는 주의해서 처리해야 함

### 수정, 삭제 벌크 연산 
쿼리 한번으로 대량의 데이터 수정

```java
// 쿼리 한번으로 이름 수정
long count = queryFactory
  .update(member)
  .set(member.username, "비회원")
  .where(member.age.lt(28))
  .execute();

// 쿼리 한번으로 기존 숫자에 1 더하기
long count = queryFactory
  .update(member)
  .set(member.age, member.age.add(1))
  .execute();

// 쿼리 한번으로 대량 데이터 삭제
long count = queryFactory
  .delete(member)
  .where(member.age.gt(18))
  .execute();
```
  - JPQL의 배치와 마찬가지로 영속성 컨텍스트에 있는 엔티티를 무시하고 실행 되기 때문에
    배치쿼리를 실행하고 나면 영속성 컨텍스트를 초기화 하는것이 안전함

---
참고 
 - 실전! Querydsl(김영한)
