---
title: Grokking Modern System Design - DNS
date: 2024-06-18 22:20:00 +0900
categories: [System Design, Grokking Modern System Design]
published: true
tags:
  - System Design
  - Grokking Modern System Design
---

### DNS란 무엇인가?

**DNS(Domain Name System)**는 인간에게 친숙한 도메인 이름을 기계가 읽을 수 있는 IP 주소에 매핑하는 인터넷의 이름 지정 서비스이다.
사용자가 브라우저에 도메인 이름을 입력하면 브라우저는 DNS 인프라에 요청하여 도메인 이름을 IP 주소로 변환해야 한다.
원하는 IP 주소를 얻으면 사용자의 요청이 대상 웹 서버로 전달된다.

![image](https://d1.awsstatic.com/Route53/how-route-53-routes-traffic.8d313c7da075c3c7303aaef32e89b5d0b7885e7c.png)

DNS에 대한 몇 가지 중요한 세부 사항은 아래와 같다.

- 이름 서버
  - DNS는 단일 서버가 아닌 수많은 서버를 갖춘 완벽한 인프라이며, 사용자의 쿼리에 응답하는 DNS 서버를 네임서버라고 함
- 리소스 레코드
  - DNS 데이터베이스는 도메인 이름과 IP 주소 매핑을 리소스 레코드(RR) 형식으로 저장
  - 일반적인 유형의 리소스 레코드표는 아래와 같음

| 유형  | 설명                                                | 이름        | 값          | 예(유형, 이름, 값)                                       |
| ----- | --------------------------------------------------- | ----------- | ----------- | -------------------------------------------------------- |
| A     | IP 주소 매핑에 대한 호스트 이름 제공                | 호스트 이름 | IP 주소     | (A, relay1.main.educative.io, 104.18.2.119)              |
| NS    | 도메인 이름에 대한 권한 있는 DNS인 호스트 이름 제공 | 도메인 이름 | 호스트 이름 | (NS, educative.io, dns.educative.io)                     |
| CNAME | 별칭에서 정식 호스트 이름으로의 매핑 제공           | 호스트 이름 | 표준 이름   | (CNAME, educative.io, server1.primary.educative.io)      |
| MX    | 별칭에서 정식 호스트 이름으로 메일 서버 매핑 제공   | 호스트 이름 | 표준 이름   | (MX, mail.educative.io, mailserver1.backup.educative.io) |

- 캐싱
  - DNS는 다양한 계층에서 캐싱을 사용하여 사용자의 요청 대기 시간을 줄임
- 계층 구조
  - DNS 서버는 계층 구조를 사용하여 확장성을 높임

![image](https://lh5.googleusercontent.com/proxy/m22boB8uncU0czqJzAZed3DBx99D5bUIGTu1Vc9N_Far9WsvRb1UNp4rxlmshv_zxoMOsz4IaOcK_tWQ9a3ohirGmvn6h3oXGsJRRF_WzPwUvrZIk9uDg7a2aQKxam8eMqTolXDZhus1TQEkpWvwQgmBCnGCSHIlq57t9SCy3JWhpk3dT6YAiOAem-rWkosfvD9UqkgX_GjcRrU)

---

참고

- [Grokking Modern System Design Interview for Engineers & Managers](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers)
