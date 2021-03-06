---
title: Effective C++ - Item.4
date: 2020-04-30 13:15:00
categories: [C++, Effective C++]
tags:
- C++
- Effective C++
- Initial
---

# 객체를 사용하기 전에 반드시 그 객체를 초기화하자

---

## 내용

### 객체의 초기화

초기화되지 않은 값을 읽도록 내버려 두면 정의되지 않은 동작이 그대로 흘러 나오게 된다. 일반적인 경우에 적당히 무작위 비트의 값을 읽고 객체의 내부가 이상한 값을 가지게 된다.

### C++에서 객체의 초기화 보장 유무

C++의 객체(변수) 초기화가 중구난방인 것은 절대 아니다. 언제 초기화가 보장되며 언제 그렇지 않은지에 대한 규칙이 명확하게 정의되어 있다. 다만 한가지 안타까운 점은, 규칙 자체가 아주 복합하다는 것이다.

일반적인 사항부터 확일해보면, C++의 C 부분만을 쓰고 있으며 초기화에 런타임 비용이 소모될 수 있는 상황이라면 값이 초기화 된다는 보장이 없다. 그렇지만 C가 아닌 부분으로 진입하게 되면 사정이 때떄로 달라지게 된다. 배열(C++의 C부분)은 각 원소가 확실히 초기화 된다는 보장이 없으나, vector(C++의 STL부분)는 그러한 보장을 가지게 되는 이유가 바로 이런 법칙 때문이다.

가장 좋은 방법은 모든 객체를 사용하기 전에 **항상** 초기화 하는 것이다.

```cpp
int x = 0;                          // int의 직접 초기화
const char* text = "Hello World!";  // 포인터의 직접 초기화

double d;                           // 입력 스트림을 통한 초기화 수행
std::cin >> d;
```

### 클래스 생성자에서의 초기화

위와 같은 변수의 초기화 부분을 제외하면, C++의 초기화의 나머지 부분은 생성자로 귀결된다. 

생성자에서 지킬 규칙은 "*객체의 모든 것을 초기화 한다*" 라는 것만 지키면 된다는 것을 기억하자. 다만, 한가지 주의할 점은 *대입(assignment)*을 *초기화(initialization)*으로 착각하지 않는 다는 것이 가장 중요하다.

```cpp
class PhoneNumber { ... };

class ABEntry {
public:
  ABEntry(const std::string& name,
          const std::string& address,
          const std::list<PhoneNumber>& phones) {

    // 아래와 같은 코드는 "초기화"가 아닌 "대입" 이다
    theName = name;
    theAddress = address;
    thePhones = phones;
    numTimesConsulted = 0;
  }

private:
  std::string theName;
  std::string theAddress;
  std::list<PhoneNumber> thePhones;
  int numTimesConsulted;
};
```

위의 예제와 같은 경우, ABEntry 객체는 원하는 값을 가지게 되지만, 그 내부를 확인해 보면 생성자에서 각 멤버변수에 대한 초기화가 진행되는 것이 아닌 대입을 하고 있다. 즉, theName, theAddress, thePhones 멤버 변수의 경우, 생성자에서 초기화 되는 것이 아니라 어떤 값이 대입됨으로써 ABEntry 생성자에 진입하기도 전에 이들 세 데이터 멤버 변수의 기본 생성자가 호출되어 진다. 다만 numTimesConsulted 멤버 변수의 경우 기본 데이터 타입인 int이므로, 생성자에서 대입되기 전에 초기화 되리란 보장이 없다.

위와같이 생성자에서 *대입* 대신 *초기화*를 하기 위해서는 *멤버 초기화 리스트*를 사용하면 된다.

```cpp
class PhoneNumber { ... };

class ABEntry {
public:
  ABEntry(const std::string& name,
          const std::string& address,
          const std::list<PhoneNumber>& phones) : 
            theName(name),           // 대입이 아닌 초기화를 한다
            theAddress(address),
            thePhones(phones),
            numTimesConsulted(0) {} // 생성자 본문은 비어있다

private:
  std::string theName;
  std::string theAddress;
  std::list<PhoneNumber> thePhones;
  int numTimesConsulted;
};
```

멤버 변수에 사용자가 원하는 값을 주고 시작한다는 점에서는 동일하지만, 위의 생성자(초기화)에서는 먼저 정의한 생성자(대입)보다 효율적일 가능성이 크다.

대입만 사용한 버전의 경우 theName, theAddress, thePhones 멤버 변수에 대해 기본 생성자를 호출해서 초기화를 미리 해 놓은 후에 생성자에서 곧바로 새로운 값을 대입하고 있다. 따라서 먼저 호출된 각 멤버 변수의 기본 생성자에서 해 놓은 초기화는 아깝게도 그냥 헛짓이 되어 버리고 만다.

이러한 문제는 멤버 초기화 리스트를 통해 피해갈 수 있다. 초기화 리스트에 들어가는 인자는 바로 멤버 변수에 대한 생성자의 인자로 쓰이기 때문이다. 두번째의 초기화 버전의 생성자의 경우, theName은 name으로 부터 복사 생성자에 의해 초기화 고,  theAddress는 address으로 부터 복사 생성자에 의해 초기화되며, thePhones도 동일하게 phones로 부터 초기화 된다.

대부분의 데이터 타입에 대해서는, 기본 생성자 호출 후에 복사 대입 연산자를 연달아 호출하는 이전의 방법보다 복사 생성자를 한 번 호출하는 쪽이 더 효율적이다.

기본제공 타입의 객체는 초기화와 대입에 걸리는 비용의 차이가 없지만, 역시 멤버 초기화 리스트에 모두 넣어 주는 쪽이 바람직하다. 또 멤버 변수를 기본 생성자로 초기화 하고 싶을 때도 멤버 초기화 리스트를 사용하는 습관을 들이는 것이 좋다.

```cpp
ABEntry::ABEntry() :
  theName(),
  theAddress(),
  thePhones(),
  numTimesConsulted(0) {}
```

기본제공 타입의 멤버를 초기화 리스트로 넣는 일이 선택이 아니라 *의무*가 될 때도 있다. 바로 멤버 변수가 상수 이거나 참조자로 되어있을 때 이다. 이는 상수와 참조자는 대입 자체가 불가능하기 때문에 반드시 초기화 리스트를 통해 초기화 되어야 한다.

### 객체를 구성하는 데이터의 초기화 순서

객체를 구성하는 데이터의 초기화 순서는 어떤 컴파일러를 막론하고 항상 똑같다.

- 기본 클래스는 파생 클래스보다 먼저 초기화 된다.
- 클래스 멤버 변수는 그들이 선언된 순서대로 초기화 된다.

위의 ABEntry 클래스를 예로들면, theName이 항상 첫 번째로 초기화 되고, theAddress가 두번째, thePhones 세번째, numTimesConsulted가 마지막으로 초기화 된다. 즉, 선언된 순서대로 초기화 된다. 만약 어쩌다 멤버 초기화 리스트에 이들이 넣어진 순서가 다르더라도(이러한 경우 컴파일은 가능하다) 초기화 순서는 그대로 이다.

### 비지역 정적 객체의 초기화 순서는 개별 번역 단위에서 정해진다

**정적 객체(static object)** 는 자신이 생성된 시점부터 프로그램이 끝날 떄 까지 살아 있는 객체를 일컫는다. 즉, 스택 객체 및 힙 기반 객체는 애초부터 정적 객체가 될 수 없다.

정적 객체의 범주에 들어가는 것들은 아래와 같다.

- *전역 객체*
- *네입스페이스 유효범위에서 정의된 객체*
- *클래스 안에서 static으로 정의된 객체*
- *함수 안에서 static으로 정의된 객체*
- *파일 유효범위에서 static으로 정의된 객체*

이중 함수 안에 있는 정적 객체는 함수에 대해서 지역성을 가지고 있으므로 **지역 정적 객체(local static object)** 이라 하고, 나머지 정적 객체 들은 **비지역 정적 객체(non local static object)** 라고도 불린다.

이러한 다섯가지의 정적 객체들은 프로그램이 끝날 때 자동으로 소멸된다. 다시 말해, main() 함수의 실행이 끝날 때 정적 객체의 소멸자가 호출된다.

*비지역 정적 객체*의 초기화 순서 문제의 시작은 컴파일에서 부터 시작된다.

**번역 단위(translation unit)** 는 컴파일을 통해 하나의 목적 파일(object file)을 만드는 바탕이 되는 소스 코드를 일컫는다. 여기서 번역은 소스의 언어를 기계어로 옮긴다는 의미이다. 기본적으로는 소스 파일 하나가 되는데, 그 파일이 #include하는 파일들까지 합쳐서 하나의 번역 단위가 된다.

별도로 컴파일된 소스 파일이 두 개 이상 있으며 각 소스 파일에 *비지역 정적 객체(전역 객체, 네임스페이스에 있는 객체, 클래스 혹은 파일에 있는 정적 객체)* 가 한개 이상 들어 있는 경우에 문제가 발생한다. 실질적인 문제는 한쪽 번역 단위에 있는 *비지역 정적 객체*의 초기화가 진행되면서 다른 쪽 번역 단위에 있는 *비지역 정적 객체*가 사용되는데, 불행히도 다른 쪽 번역 단위에 있는 객체가 초기화되어 있지 않을 지도 모른다는 점이다. 이유는 **별개의 번역 단위에서 정의된 *비지역 정적 객체*들의 순서는 '정해져 있지 않다'** 라는 사실 때문이다.

```cpp
// 사용자가 사용하는 라이브러리에 포함된 클래스
class FileSystem {
public:
  // 멤버 함수들 

  ...

  std::size_t numDisks() const();

  ...

};

// 사용자가 쓰게 될 객체
extern FileSystem tfs;
```

```cpp
// 사용자가 정의한 클래스
class Direcotry {
public:
  Direcotry( params ) {

    ...

    // 외부 파일에 정의한 tfs 객체를 사용
    std::size_t disks = tfs.numDisks();

    ...

  }

  ...

};

...

// 임시 파일을 담는 디렉토리
Direcotry tempDir( params );

```

위의 예제에서, 정적 객체의 초기화 순서 때문에 문제가 심각해질 수도 있는 상황이 들어났다. tfs가 tempDir보다 먼저 초기화 되지 않으면, tempDir의 생성자는 tfs가 초기화되지도 않았는데 tfs를 사용하려고 할 것이다. 그러나 tfs와 temDir은 제작자도, 만들어진 시기도, 소스파일 또한 다르다. 즉, 이들은 다른 *번역 단위* 안에서 정의된 *비지역 정적 객체* 이다.

tempDir 전에 tfs가 초기화되게 만들고 싶을때, 이 목표를 어떻게 달성할 것인가. 결론부터 이야기 하자면 불가능 하다. **서로 다른 번역 단위에 정의된 *비지역 정적 객체*들 사이의 상대적인 초기화 순서는 정해져 있지 않기** 때문이다.

위의 문제를 해결하는 방법은 초기화 순서 문제를 사전에 봉쇄하는 것이다. *비지역 정적 객체*를 하나씩 맡는 함수를 준비하고 이 안에 객체를 넣는 방법이다. 함수 속에서도 이들은 정적 객체로 선어하고, 그 함수에서는 이들에 대한 참조자를 반환하는 것이다. 사용자 쪽에서는 *비지역 정적 객체*를 직접 참조하는 것이 아닌 함수 호출로 참조하도록 하는 것이다. 즉, *비지역 정적 객체*를 *지역 정적 객체*로 바꾸는 것이다. 이는 디자인 패턴중 *Singleton Pattern*의 전형적인 구현양식과 동일하다.

*지역 정적 객체*는 함수 호출중에 그 객체의 정의에 최초로 닿았을 때 초기화되도록 만들어져 있다. 이는 C++에서 보장하는 사실이며, 위의 방법은 그 사실을 이용한 방법이다. 따라서 *비지역 정적 객체*를 직접 접근하지 않고 *지역 정적 객체*에 대한 참조자를 반환하는 쪽으로 바꾸었다면, 얻어낸 참조자는 반드시 초기화된 객체를 참조하도록 맞춰주어야 한다.

*비지역 정적 객체* 대신에 준비한 '*지역 객체 참조자 반환*' 함수를 호출할 일이 없다면 해당 객체의 생성/소멸 비용도 생기지 않도록 막아야 한다.

```cpp
// 이전과 같은 클래스
class FileSystem { ... };

// tfs 객체를 함수로 대체한다
// 이 함수는 클래스안에 정적 멤버로 들어가도 된다
FileSystem& tfs() {
  // 지역 정적 객체를 정의하고 초기화
  static FileSystem fs;

  // 이 객체에 대한 참조자를 반환
  return fs;
};
```

```cpp
// 이전과 같은 클래스
// 다만 tfs가 함수로 바뀌었으므로 이제는 함수호출로 바뀜
class Direcotry { ... };

// tempDir 객체를 함수로 대체한다
// 이 함수는 클래스안에 정적 멤버로 들어가도 된다
Direcotry& tempDir() {
  // 지역 정적 객체를 정의하고 초기화
  static Directory td;

  // 이 객체에 대한 참조자를 반환
  return td;
};

```

위의 경우에 대한 문제점을 생각했을 때, 참조자 반환 함수는 내부적으로 정적 객체를 쓰기 때문에, 다중스레드 시스템에서는 동작에 장애가 생길 수도 있다. 다중스레드 시스템상에서는 *비상수 정적 객체(지역 객체이든 비지역 객체이든)* 은 온갖 문제를 발생 시킬수 있다. 이 문제를 다루기 위한 한가지 방법으로 프로그램이 다중스레드로 돌입하기 전의 시동 단계에서 참조자 반환 함수를 전부 직접 호출해 줄 수 있다. 이렇게 하면 초기화에 관계된 *경쟁 상태(race condition)* 가 없어지게 된다.

물론 초기화 순서 문제를 방지하기 위해 이처럼 참조자 반환 함수를 사용하는 아이디어는 객체들의 초기화 순서를 제대호 맞춰 둔자는 전제조건이 뒷바침되어야 한다. 이를테면 객체 B가 초기화되기 전에 객체 A가 초기화 되어야 하는데, A의 초기화가 B의 초기화에 의족하도록 되어 있다면 이는 제대로 동작하지 않을 것이다. 

## 요점

> 기본제공 타입의 객체는 경우에 따라 저절로 되기도 하고 안되기도 하기 때문에 직접 초기화한다.

> 셍성자에서는, 데이터 멤버에 대한 대입문을 생성자 본문 내부에 넣는 방법으로 멤버를 초기화 하지 말고 멤버 초기화 리스트를 사용한다.

> 초기화 리스트에 데이터 멤버를 나열할 때는 클래스에 각 데이터 멤버가 선언된 순서와 똑같이 나열한다.

> 여러 번역 단위에 있는 비지역 정적 객체들의 초기화 순서 문제는 피해서 설계해야 한다.

> 비지역 정적 객체를 지역 정적 객체로 바꾼다.

---
---
*참고*. Effective C++ 3/E - Scott Meyers

---
