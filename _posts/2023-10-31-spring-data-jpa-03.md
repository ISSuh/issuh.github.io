---
title: Spring Data JPA - 확장 기능
date: 2023-10-31 14:10:00 +0900
categories: [Backend, JPA]
published: true
tags:
  - Web
  - Spring Framework
  - DataBase
  - JPA
  - Spring Data JPA
---

### 사용자 정의 리포지토리 구현

- 스프링 데이터 JPA 리포지토리는 인터페이스만 정의하고 구현체는 스프링이 자동 생성
- 스프링 데이터 JPA가 제공하는 인터페이스를 직접 구현하면 구현해야하는 기능이 너무 많음

스프링이 생성해주는 구현이외에 사용자가 직접 구현하고자 할 때는 다음과 같은 방멉이 있음.

```java
// 사용자 정의 인터페이스
public interface MemberRepositoryCustom {
  List<Member> findMemberCustom();
}
```

```java
// 사용자 정의 인터페이스 구현
@RequiredArgsConstructor
public class MemberRepositoryCustomImpl implements MemberRepositoryCustom {

  private final EntityManager em;

  @Override
  public List<Member> findMemberCustom() {
    return em.createQuery("select m from Member m", Member.class)
                .getResultList();
  }
}
```

```java
// 사용자 정의 인터페이스 상속
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
}
```

- 사용자 정의 구현 클래스
  - 규칙 : 리포지토리 인터페이스 이름 + Impl
  - 스프링 데이터 JPA가 인식하여 스프링 빈으로 등록
- 실무에서는 주로 QueryDSL이나 SpringJdbcTemplate을 함께 사용할 떄 사용자 정의 리포지토리 기능 사용

항상 사용자 정의 리포지토리가 필요한 것은 아니고 그냥 임의의 리포지토리를 만들어도 됨.
예를들어 MemberQueryRepository를 인터페이스가 아닌 클래스로 만들고 스프링 빈으로 등록해서
그냥 직접 사용해도 됨. 물론 이 경우 스프링 데이터 JPA와는 아무런 관계 없이 별도로 동작 함.

### Auditing

엔티티를 생성, 변경할 떄 자동으로 시간 및 변경자 추척하는 방법

```java
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseTimeEntity {
    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdDate;
    @LastModifiedDate
    private LocalDateTime lastModifiedDate;
}
```

```java
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseEntity extends BaseTimeEntity {
    @CreatedBy
    @Column(updatable = false)
    private String createdBy;
    @LastModifiedBy
    private String lastModifiedBy;
}
```

```java
public class Member extends BaseEntity {
  ...
}
```

```java
@EnableJpaAuditing
@SpringBootApplication
public class DataJpaApplication {

  public static void main(String[] args) {
    SpringApplication.run(DataJpaApplication.class, args);
  }

  // 생성자, 변경자의 이름을 생성하기 위한 메소드
  @Bean
  public AuditorAware<String> auditorProvider() {
    return () -> Optional.of(UUID.randomUUID().toString());
  }
}
```

- @EnableJpaAuditing 어노테이션을 스프링 부트 설정 클래스에 적용해야 함
- EntityListeners(AuditingEntityListener.class) 어노테이션은 엔티티에 적용 해야함
- 실무에서는 보통 세션 정보나, 스프링 시큐리티 로그인 정보에서 ID를 받음

EntityListeners(AuditingEntityListener.class) 어노테이션을 생략하고 스프링 데이터 JPA가 제공하는 이벤트를
엔티티 전체에 적용하려면 orm.xml에 다음과 같이 등록하면 됨

```xml
<!-- META-INF/orm.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<entity-mappings xmlns="http://xmlns.jcp.org/xml/ns/persistence/orm"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence/orm http://xmlns.jcp.org/xml/ns/persistence/orm_2_2.xsd"
                 version="2.2">
    <persistence-unit-metadata>
        <persistence-unit-defaults>
            <entity-listeners>
                <entity-listener class="org.springframework.data.jpa.domain.support.AuditingEntityListener"/>
            </entity-listeners>
        </persistence-unit-defaults>
    </persistence-unit-metadata>
</entity-mappings>
```

### Web 확장 - 페이징과 정렬

스프링 데이터가 제공하는 페이징과 정렬 기능을 스프링 MVC에서 편하게 사용 가능

```java
@GetMapping("/members")
public Page<Member> list(Pageable pageable) {
  Page<Member> page = memberRepository.findAll(pageable);
  return page;
}
```

- 파라미터로 Pageable을 받을 수 있음
- Pageable은 인터페이스로, 실제로는 org.springframework.data.domain.PageRequest 객체 생성

**요청 파라미터**

- 예시 : /members?page=0&size=3&sort=id,desc&sort=username,desc
- page : 현재 페이지. **0부터 시작함**.
- size : 한 페이지에 노출할 데이터 건수.
- sort : 정렬 조건을 정의. (ASC | DESC)

기본값에 대한 설정은 아래와 같이 글로벌, 개별 설정 두가지 방법 존재

- 기본 값

```
spring.data.web.pageable.default-page-size=20 /# 기본 페이지 사이즈/
spring.data.web.pageable.max-page-size=2000 /# 최대 페이지 사이즈/
```

- 개별 설정

```java
@GetMapping("/members")
public Page<Member> list(
  @PageableDefault(size = 12, sort = "username",
                   direction = Sort.Direction.DESC) Pageable pageable) {
  Page<Member> page = memberRepository.findAll(pageable);
  return page;
}
```

#### Page 내용을 DTO로 변환

엔티티를 API로 노출하면 다양한 문제가 생김. 그러므로 엔티티는 반드시 DTO로 변환해서 반환해야 함.
Page는 map()을 지원하여 내부 데이터를 다른것으로 변경 가능

```java
@Data
public class MemberDto {
    private Long id;
    private String username;
    public MemberDto(Member m) {
        this.id = m.getId();
        this.username = m.getUsername();
    }
}
```

```java
// Page.map() 사용
@GetMapping("/members")
public Page<MemberDto> list(Pageable pageable) {
    Page<Member> page = memberRepository.findAll(pageable);
    Page<MemberDto> pageDto =
      page.map(member -> new MemberDto(member));
    return pageDto;
}

// Page.map() 코드 최적화
@GetMapping("/members")
public Page<MemberDto> list(Pageable pageable) {
    return memberRepository.findAll(pageable).map(MemberDto::new);
}
```

#### Page를 1부터 시작하기

스프링 데이터는 Page를 0부터 시작 함.
만약 1부터 시작하려면 아래와 같은 두가지 방법이 있음

- Pageable, Page를 파라미터와 응답 값으로 사용하지 않고, 직접 클래스를 만들어서 처리하고,
  직접 PageReqeust(Pageable 구현체)를 생성하여 리포지토리에 전달.
  - 응답값도 Page 대신 직접 만들어서 제공해야 함
- spring.data.web.pageable.one-indexed-parameters을 true로 설정
  - 이 방법은 web에서 page 파라미터를 -1 할뿐이며,
    응답값인 page 이외 다른 필드는 모두 0 페이지 인덱스를 기준으로 값을 설정하는 한계가 있음

---

참고

- 실전! 스프링 데이터 JPA(김영한)
