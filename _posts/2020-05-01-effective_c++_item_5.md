---
title: Effective C++ - Item.5
date: 2020-05-01 12:08:00
categories: [C++, Effective C++]
tags:
- C++
- Effective C++
---

# C++가 은근슬쩍 만들어 호출해 버리는 함수들에 촉각을 세우자

---

## 내용

### 컴파일러가 저절로 선언해주는 멤버 함수

C++의 어떤 멤버 함수는 클래스 안에 직접 선언해 주지 않으면 컴파일러가 저절로 선언해 주도록 되어 있다. 바로 *복사 생성자(Copy Constructor)*, *복사 대입 생성자(Copy Assignment Operator)*, *소멸자(Destructor)* 이다. 이러한 컴파일러가 저절로 만들어주는 함수의 형태는 모두 기본형이다. 그리고, 생성자조차도 선언되어 있지 않으면 역시 컴파일러가 프로그래머를 대신하여 기본 생성자를 선언해 놓는다. 이들은 모두 public 멤버이며 inline 함수 이다. 

```cpp
// 두 클래스는 근본적으로 같다.

class Empty {};

class Empty {
public:
  // 기본 생성자
  Empty() { ... }

  // 복사 생성자
  Empty(const Empty& rhs) { ... }

  //  소멸자
  ~Empty() { ... }

  // 복사 대입 연산자
  Empty& operator=(const Empty& rhs) { ... }  
};
```

이들은 꼭 필요하다고 컴파이러가 판단할 때만 만들어 진다.

```cpp
  Empty e1;       // 기본 생성자, 소멸자
  Empty e2(e1);   // 복사 생성자

  e2 = e1;        // 복사 대입 연산자
```

#

기본 생성자와 소멸자가 하는 일은 일차적으로 컴파일러에게 "배후의 코드"를 깔 수 있는 자리를 마련하는 것이다. 기본 클래스 및 비정적 데이터 멤버의 생정자와 소멸자를 호출하는 코드가 여기서 생기게 된다. 이때, 소멸자는 이 클래스가 상속한 기본 클래스의 소멸자가 가상 소멸자로 되어 있지 않으면 역시 비가상 소멸자로 만들어지게 되니 이를 주의해야 한다.

컴파일러가 만들어낸 복사 생성자, 복사 대입 연산자가 하는 일은, 원본 객체의 비정적 데이터를 사본 객체 쪽으로 그냥 복사하는 것이 전부 이다.

```cpp
template<class T>
class NameObject {
public:
  NameObject(const char* name, const T& value);
  NameObject(const std::string& name, const T& value);

  ...

private:
  std::string nameValue;
  T objectValue;
};
```

이 NameObject 템플릿 안에는 생성자가 성언되어 있으므로, 컴파일러는 기본 생성자를 만들어내지 않을 것이다. 즉, 만약 생성자 인자가 꼭 필요한 클래스를 만드는 것이 프로그래머의 결정이고 그렇게 했다면, 인자를 받지 않는 생성자를 컴파일러가 만들 걱정은 하지 않아도 된다.

반면, 복사 생성자나 복새 대입 연산자는 NameObject에 선언되어 있지 않기 때문에, 이 두 함수의 기본형이 컴파일러에 의해 만들어진다.

```cpp
NameObject<int> no1("Hello World");
NameObject<int> no2(no1);           // 복사 생성자 호출
```

컴파일러가 만들어낸 복사 생성자는 no1.nameValue와 no1.objectValue를 사용하여 no2.nameValue와 no2 objectValue를 각각 초기화해야 한다.

nameValue의 타입은 std::string 인데, 표준 string 타입은 자체적으로 복사 생성자를 가지고 있으므로 no2.nameValue의 초기화는 string 복사 생성자에 no1.nameValue를 인자로 넘겨 호출함 으로써 이루어지게 된다.

NameObject<int>::objectValue의 타입은 현재의 템플릿 인스턴스화에서 T를 int로 선언하였으므로 int인데, int는 기본제공형 타입이므로 no2.objectValue의 초기화는 no1.objectValue의 각 비트를 그대로 복사해 오는것으로 끝난다.

컴파일러가 만들어 주는 NmaeObject<int>의 복사 대입 연산자도 근본적으로는 동작 원리가 같다. 하지만 일반적인것을 보면, 이 복사 대입 연산자의 동작이 위의 설명대로 되려면 최종 결과 코드가 *'적법해야(legal)'* 해야 하고, *'합당해야(resonable)'* 한다. 둘 중 어느 검사도 통과하지 못한다면 컴파일러는 operator=의 자동생성을 거부한다.

```cpp
class NameObject {
public:
  NameObject(std::string& name, const T& value);

  ...

private:
  std::string& nameValue;
  const T objectValue;
};

...

std::string newDog("NewDog");
std::string oldDog("OldDog");

NameObject<int> n(newDog, 2);

NameObject<int> o(oldDog, 36);

p = s;                          // p에 들어있는 데이터 멤버에서 일어난 일?
```

대입 연산이 일어나기 전, n.nameValue 및 o.nameValue는 string 객체를 참조하고 있다. 물론 같은 string 객체는 아니다. 이때 대입 연산이 일어나면 n.nameValue가 어떻게 되어야 하는가? 

o.nameValue가 참조하는 string을 가리켜야 하는가? 즉, 참조자 자체가 바뀌어야 하는 건가? 이는 불가능 하다. C++의 참조자는 원해 자신이 참조하고 있는 것과 다른 객체를 참조할 수 없기 때문이다.

그렇다면 n.nameValue가 참조하는 string 객체 자체가 바뀌는 것이 맞는건가? 이렇게 되면 그 string에 대한 포인터나 참조자를 품고 있는 다른 객체들, 즉 실제 대입 연산에 직접적으로 관여하지 않는 객체까지 영향을 받게 된다.

어느쪽은 정하더라도 이 문제에 대해 컴파일러는 오류를 발생시킨다. 그렇기 때문에 참조자를 데이터 멤버로 가지고 있는 클레스에 대입 연산을 지원하려면 프로그래머가 직접 복사 대입 연산자를 정의해 주어야 한다.

데이터 멤버가 상수 객체인 경우(위 예제에서의 objectValue 멤버와 같은 경우)에도 컴파일러가 비슷하게 동작한다. 상수 멤버를 수정하는 것은 문법에 어긋나기 때문에, 자동으로 컴파일러에 의해 만들어진 암시적 복사 대입 연산자 내부에서는 상수 멤버를 어떨게 처리해야할지 애매해지게 된다.

추가적인 결정적인 포인트는, 복사 대입 연산자를 private로 선언한 기본 클래스로 부터 파생된 서브클래스의 경우, 이 클래스는 암시적 복사 대입 연산자를 가질수 없게 된다. 컴파일러가 거부하기 때문이다. 파생 클래스에 대해 컴파일러가 만들어 주는 복사 대입 연산자는 기본 클래스 부분을 맡도록 되어 있긴 하지만, 이렇게 하더라도 파생 클래스 쪽에서 호출할 권한이 없는 멤버 함수는 암시적 복사 대입 연산자에서 호출할 수는 없기 때문이다.

## 요점

> 컴파일러는 경우에 따라 클래스에 대해 기본 생성자, 복사 생성자, 복사 대입 연산자, 소멸자를 암시적으로 만들어 놓을 수 있다.

---
---
*참고*. Effective C++ 3/E - Scott Meyers

---
