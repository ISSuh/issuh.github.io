---
title: Grokking Modern System Design - 비기능적 시스템 특성
date: 2024-06-18 22:10:00 +0900
categories: [System Design, Grokking Modern System Design]
published: true
tags:
  - System Design
  - Grokking Modern System Design
---

### 가용성이란 무엇인가?

가용성은 클라이언트가 일부 서비스나 인프라에 액세스할 수 있고 정상적인 조건에서 작동되는 시간의 비율이다.
예를 들어, 서비스 가용성이 100% 라면 해당 서비스가 항상 의도한 대로 작동하고 응답(정상적으로 작동)한다는 의미이다.

### 가용성 측정

수학적으로 가용성 $A$ 는 비율이다.
$A$ 값이 높을수록 좋으며 이는 공식으로 나타낼 수 있다.

$A = (총 시간 - 시스템이 멈춘 시간)/총시간 * 100$

일반적으로 가용성을 9의 숫자로 측정한다.
다음 표는 주어진 숫자 9를 사용할 때 허용되는 가동 중지 시간이다.

| 가용성(%) | 연간 가동 중지 시간 | 월별 가동 중지 시간 | 주당 가동 중지 시간 |
| --------- | ------------------- | ------------------- | ------------------- |
| 90%       | 36.5일              | 72시간              | 16.5시간            |
| 99%       | 3.65일              | 7.20시간            | 1.68시간            |
| 99.5%     | 1.83일              | 3.60시간            | 50.4분              |
| 99.9%     | 8.76시간            | 43.8분              | 10.1분              |
| 99.99%    | 52.56분             | 4분 32초            | 1분 01초            |
| 99.999%   | 5분 26초            | 25.9초              | 6.05초              |
| 99.9999%  | 31.5초              | 2.59초              | 0.605초             |
| 99.99999% | 3.15초              | 0.259초             | 0.0605초            |

### 신뢰성

신뢰성(Reliability)은 서비스 $R$이 지정된 시간 동안 기능을 수행할 확률이다.
$R$은 다양한 운영 조건에서 서비스가 어떻게 수행되는지 측정한다.

우리는 종종 **MTBF(평균 고장 간격)** 와 **MTTR(평균 수리 시간)** 을 $R$을 측정할 지표로 사용한다.

$MTBF = (총 경과 시간 - 시스템이 멈춘 시간의 합) / 전체 장애 횟수$
$MTTR = 전체 수리 시간 / 전체 수리 횟수$

우리는 더 높은 MTBF 값과 더 낮은 MTTR 값을 위해 노력해야 한다.

### 신뢰성 및 가용성

안정성과 가용성은 합의된 서비스 수준 목표(SLO)에 대한 서비스 규정 준수를 측정하는 두 가지 중요한 지표이다.

가용성 측정은 시간 손실에 의해 결정되는 반면, 오류의 빈도와 영향은 안정성 측정에 영향을 미친다다.
가용성과 안정성은 이해관계자가 서비스 상태를 평가할 수 있도록 하기 때문에 필수적이다.

신뢰성($R$)과 가용성($A$)은 별개의 개념이지만 서로 연관되어 있다.
수학적으로 $A$는 $R$의 함수이다.
즉, $R$ 의 값은 독립적으로 변경될 수 있으며 $A$의 값은 $R$에 따라 달라진다.
따라서 다음과 같은 상황이 발생할 수 있다.

- low $A$, low $R$
- low $A$, high $R$
- high $A$, low $R$
- high $A$, high $R$

![image](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT8sPX1_6j6xGYAABympC-5sYRXPvYvu-Xo-Q&s)

### 확장성이란 무엇인가?

확장성은 성능 저하 없이 증가하는 작업량을 처리할 수 있는 시스템의 능력이다.
예를 들어, 검색 엔진은 늘어나는 사용자 수와 인덱싱하는 데이터의 양을 수용해야 한다.

워크로드는 다음을 포함하여 다양한 유형일 수 있습니다.

- 요청 작업량 : 시스템에서 처리하는 요청 수
- 데이터/스토리지 작업량 : 시스템에 저장되는 데이터의 양

#### Dimensions

확장성의 다양한 차원은 다음과 같습니다.

- 크기 확장성
  - 시스템에 사용자와 리소스를 추가하기만 하면 시스템 크기를 확장할 수 있어야 함
- 관리 확장성
  - 점점 더 많은 조직이나 사용자가 단일 분산 시스템을 쉽게 공유할 수 있는 있어야 함
- 지리적 확장성
  - 시스템은 더 작은 지역뿐만 아니라 넓은 지리적 지역에도 쉽게 서비스를 제공할 수 있어야 함
  - 수용 가능한 성능 제약 조건을 유지하면서 프로그램이 다른 지역에 얼마나 쉽게 서비스를 제공할 수 있는지와 관련됨

#### 확장성에 대한 다양한 접근 방식

확장성을 구현하는 다양한 방법은 다음과 같다.

- 수직적 확장
  - 기존 장치에 하드웨어적으로 컴퓨팅 파워를 올려서 확작하는 방법
- 수평적 확장
  - 네트워크의 머신수를 늘려서 확장하는 방법

![image](https://hudi.blog/static/8f119ed1d4e0f2093247686e69595edf/d376a/scale-up-scale-out.png)

### 유지 관리성이란 무엇인가?

시스템 구축 외에도 이후의 주요 작업 중 하나는 버그를 찾아서 수정하고, 새로운 기능을 추가하고, 시스템 플랫폼을 최신 상태로 유지하고, 원활한 시스템 운영을 보장하여 시스템을 계속 가동하는 것이다.
시스템 설계의 이러한 요구 사항을 정의하는 두드러진 특징 중 하나는 **유지 관리 가능성** 이다.
유지 관리 가능성의 개념을 세 가지 기본 측면으로 더 나눌 수 있다.

- 운용성
  - 정상적인 상황에서 시스템의 원활한 작동을 보장하고 오류가 발생한 경우 쉽게 회복할 수 있어야 함
- 명료성(Lucidity)
  - 코드의 단순성을 나타냄
  - 코드 베이스가 단순할수록 이해하고 유지 관리하기가 더 쉬워짐
- 수정 가능성(Modifiability)
  - 수정된 기능, 새로운 기능, 예상치 못한 기능을 번거로움 없이 통합할 수 있어야 함

#### 유지보수성 측정

**유지보수성(Maintenanceability)**은 서비스 $M$이 장애 발생 후 지정된 시간 내에 기능을 복원할 확률이다.
서비스 $M$이 얼마나 편리하고 신속하게 정상적인 작동 상태를 회복하는지 측정한다.

일반적으로 측정하는 지표로 MTTR(평균 수리 시간)을 사용한다.

$MTTR = 전체 수리 시간 / 전체 수리 횟수$

### 내결함성이란 무엇인가?

실제 대규모 애플리케이션은 수백 대의 서버와 데이터베이스를 실행하여 수십억 명의 사용자 요청을 수용하고 중요한 데이터를 저장한다.
이러한 애플리케이션에는 데이터 안전을 돕고 단일 실패 지점을 방지하여 계산 집약적인 작업의 재계산을 피하는 메커니즘이 필요하다.

**내결함성**은 하나 이상의 구성 요소에 장애가 발생하더라도 시스템이 지속적으로 실행되는 능력을 나타낸다.
여기서 구성요소는 소프트웨어일 수도 있고 하드웨어일 수도 있다.
100% 내결함성을 갖춘 시스템을 구상하는 것은 사실상 매우 어려운 일이다.

#### 내결함성 기술

내결함성은 시스템 구조를 고려하여 다양한 접근 방식을 통해 달성할 수 있다.

##### 복제

가장 널리 사용되는 기술 중 하나는 **복제 기반 내결함성** 이다.
이 기술을 사용하면 서비스와 데이터를 모두 복제할 수 있다.
실패한 노드를 정상 노드로 교체하고 실패한 데이터 저장소를 복제본으로 교체할 수 있다.
대규모 서비스는 최종 고객에게 영향을 주지 않고 투명하게 전환할 수 있다.

별도의 저장소에 데이터의 여러 복사본을 만들고, 데이터 업데이트가 발생할 때 일관성을 위해 모든 복사본을 정기적으로 업데이트해야 한다.
시스템에 강력한 일관성이 필요한 경우 복제본의 데이터를 동기식으로 업데이트할 수 있지만 이로 인해 시스템 가용성이 감소된다.
또한 최종 일관성을 허용할 수 있는 경우 복제본의 데이터를 비동기식으로 업데이트할 수 있으므로 모든 복제본이 수렴될 때까지 오래된 읽기가 발생할 수 있다.
두 일관성 접근 방식 간에는 상충 관계가 있으므로 장애 시 가용성혹은 일관성을 선택하여 적절히 타협해야한다.

![image](https://www.enterprisestorageforum.com/wp-content/uploads/2021/02/understanding-fault-tolerance-securing-your-system_6019c7b9d29a4.png)

##### 체크 포인트

체크포인트는 오류나 서비스 중단으로 인해 장애가 발생한 경우 나중에 검색할 수 있도록 시스템 상태를 안정적인 저장소에 저장하는 기술이다.

체크포인트는 다양한 시간 간격으로 여러 단계에서 수행되는 내결함성 기술이다.
분산 시스템에 장애가 발생하면 이전 체크포인트에서 마지막으로 계산된 데이터를 가져와서 거기서부터 작업을 시작할 수 있다.

체크포인트는 시스템의 실제 실행에 대한 전역 상태를 나타내는 방식으로 시스템의 다양한 개별 프로세스에 대해 수행되며 상태에 따라 체크포인트를 두 가지 유형으로 나눌 수 있다.

- 일관된 상태
  - 시스템의 모든 개별 프로세스가 공유 상태 또는 시스템에서 발생한 이벤트 순서에 대해 일관된 보기를 갖는 상태
  - 일관된 상태에서 생성된 스냅샷에는 일관된 상태의 데이터가 포함되어 시스템의 가능한 상황을 나타냄
- 불일치 상태
  - 시스템의 여러 프로세스의 저장된 상태에 불일치가 있는 상태
  - 서로 다른 프로세스에 걸친 체크포인트가 일관되고 조정되지 않음

![image](https://images.slideplayer.com/26/8338442/slides/slide_9.jpg)

---

참고

- [Grokking Modern System Design Interview for Engineers & Managers](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers)