---
title: Effective C++ - Item.3
date: 2020-04-30 11:23:00
categories: [C++, Effective C++]
tags:
- C++
- Effective C++
- Const
---

# 낌새만 보이면 *const*를 들이대 보자!

---

## 내용

### Const 키워드

*const*의 가장 눈에 띄는 특징이라면, *const*가 붙은 객체는 외부 변경을 **불가능** 하게 한다라는 '의미적인 제약'을 소스코드 수준에서 행해진다는 점과 컴파일러가 이 제약을 단단히 지켜준다는 점이다.

*const* 키워드는 클래스 바깥에서 전역 혹은 네임스페이스 유효범위의 상수를 선언하는데도 쓸 수 있으며, 파일, 함수, 블록 유효범위에서 *static*으로 선언한 객체에도 *const*를 붙일 수 있다. 클래스 내부의 경우에는 정적 멤버 및 비정적 멤버 모두를 상수로 선언 할 수 있다. 뿐만 아니라 포인터 역시 포인터 자체를 상수로, 혹은 포인터가 가리키는 값을 상수로, 아니면 둘다 상수로 선언하는 것이 가능하다.

```cpp
char greeting[] = "Hellp";        // 비상수 포인터
char* p = greeting;               // 비상수 데이터

const char* p = greeting;         // 비상수 포인터,
                                  // 상수 데이터

char* const p = greeting;         // 상수 포인터,
                                  // 비상수 데이터

const char* const p = greeting;   // 상수 포인터,
                                  // 상수 데이터
```

### STL Iterator의 Const

STL의 반복자(Iterator)는 포인터를 본뜬 것이기 때문에, 기본적인 동작 원리가 T* 포인터와 매우 흡사하다. 어떤 반복자를 *const*로 선언하는 일은 포인터를 const T* 와 같이 상수로 선언하는 것과 같다. 반복자는 자신이 가리키는 대상이 아닌 것을 가리키는 경우가 허용되지 않지만, 반복자가 가리키는 대상 자체는 변경이 가능하다. 만약 변경이 불가능한 객체를 가리키는 반복자가 필요하다면 *const_iterator*를 사용하면 된다.

```cpp
std::vector<int> vec;

// iter는 'T* const' 처럼 동작한다
const std:::vector<int>::iterator iter = vec.begin();

*iter = 10;   // 성공, iter가 가리키는 대상을 변경한다
++iter;       // 에러, iter는 상수이다

// citer는 'const T*' 처럼 동작한다
const std:::vector<int>::const_iterator citer = vec.begin();

*citer = 10;  // 에러, *citer가 상수이기 때문에 대상의 변경이 불가능하다
++citer;      // 성공, 대상이 아닌 자신을 변경하므로 가능하다
```

### 함수 선언에서의 Const

*const*의 가장 강력한 용도는 함수 선언에 사용할 경우이다. 함수 선언문에 있어서 *const*는 함수 반환 값, 각각의 매개변수, 멤버 함수 앞에 붙을 수 있고, 함수 전체에 대해 *const*의 성질을 붙일 수 있다.

함수 반환 값을 상수로 정해 주면, 안정성이나 효율을 포기하지 않고도 사용자측의 에러 발생 상황을 줄이는 효과를 줄 수 있다.

```cpp
class Rational { ... };

const Rational operator* (const Rational& lhs, const Rational& rhs);

...

// 프로그래머의 실수에 대해 사전에 방지

Rational a,b,c;


(a * b) = c;    // a*b의 결과에 operator= 호출

if((a * b) = c) // 비교문 오타로 인해 잘못된 연산 호출
```

*const* 매개변수는 단순히 *const* 타입의 지역 객체과 특성이 같다. 물론 가능한 항상 사용하는 것이 좋다. 매개변수, 혹은 지역 객체를 수정할 수 없게 하는 것이 목적이라면, *const*로 선언하는 것을 잊어버리면 안된다.

### 상수 멤버 함수

멤버 함수에 붙은 *const* 키워드의 역할은 "*해당 멤버 함수가 상수 객체에 대해 호출될 함수이다*" 라는 사실을 알려 주는 것이다.

실제 프로그램에서 상수 객체가 생기는 경우는 아래와 같다.

- 상수 객체에 대한 *포인터*로 객체가 전달될 떄
- 상수 객체에 대한 *참조자*로 객체로 전달될 때

이러한 상수 멤버 함수가 중요한 이유는 아래와 같이 두가지가 있다.

- 클래스의 인터페이스를 이해하기 좋게 해준다
  - 클래스로 만들어진 객체를 변경할 수 있는 함수는 무엇이고, 또 변경할 수 없는 함수는 무엇인가를 사용자측에서 알고 있어야 한다.
- *const* 키워드를 통해 상수 객체를 사용할 수 있게 한다
  - C++ 프로그램의 실행 성능을 높히는 핵심 기법중 하나는 객체 전달을 "*상수 객체에 대한 참조자(Reference to const)*"로 진행하는 것인데, 이 기법이 제대로 동작하려면 상수 상태로 전달된 객체를 조작할 수 있는 *const* 멤버 함수가 준비되어 있어야 한다.



C++의 *const*에 대하여 굉장히 중요한 성질중 하나는, "**const* 키워드가 있고 없고의 차이만 있는 멤버 함수들은 오버로딩이 가능하다*" 라는 점이다.


```cpp
class TextBook {
public:

  ...

  // 상수 객체에 대한 operator[]
  const char& operator[](std::size_t pos) const {
    return text[pos];
  }
  
  // 비상수 객체에 대한 operator[]
  const char& operator[](std::size_t pos) const {
    return text[pos];
  }

private:
  std::string text;
};

void print(cosnt TextBook& ctb) {
  // 상수 멤버를 호출
  std::cout << ctb[0];

  ...

}

...

TextBook tb("Hello");
TextBook ctb("World");

// 비상수 버전의 TextBook 객체를 읽는다
std::cout << tb[0];

// 비상수 버전의 TextBook 객체를 사용한다
tb[0] = 'x';

// 상수 버전의 TextBook 객체를 읽는다
std::cout << ctb[0];

// 컴파일 에러, 상수 버전의 TextBook 객체에 대한 쓰기는 금지되어 있다
// 
ctb[0] = 'x';

```

operator[]를 오버로드하여 각 버전마다 변환 타입을 다르게 가져갔기 때문에, "TextBook"의 상수 객체과 비상수 객체의 쓰임새가 달라지게 된다.

위 예제에서 주의할 점은, "ctb[0] = 'x';" 부분에서 발생한 에러는 *const char&* 타입에 대입 연산을 시도했기 때문에 생긴 순전히 operator[]의 *반환 타입(return type)* 때문이다. 즉, oprtator[] 호출이 잘못된 점은 없다.

또한 한가지 더 주의할 점은, operator[]의 비상수 멤버는 char의 *참조자(Reference)* 를 반한한다는 것인데, char 하나만 쓰면 안된다는 점이다. 만약 operator[]가 그냥 char을 반환하게 되어 있다면 다음과 같은 문장은 컴파일 되지 않을 것이다.

```cpp
tb[0] = 'x';
```

이는 기본제공 타입을 반환하는 함수의 반환 값을 수정하는 일은 절대로 없기 때문이다. 만약 이것이 합법적으로 통한다고 하더라도, 반환시 "*값의 의한 반환*"을 수행하는 기본 성질이 존재한다. 즉, 수정되는 값은 tb.text[0]의 **사본**이지 tb.text[0] 자체가 아니다.

### *비트수준 상수성(Bitwise Constness)*과 *논리수준 상수성(Logical Constness)*

어떤 멤버 함수가 상수 멤버라는 것이 어떤 의미인가 라고 할때, 크게 두가지 개념이 자리잡고 있다.

- **비트수준 상수성(Bitwise Constness)**
  - **물리적 상수성(Physical Constness)**라고도 한다
  - 어떤 멤버 함수가 그 객체의 어떤 데이터 멤버도 건드리지 않아야 그 멤버 함수가 '*const*' 임을 인정
  - 객체를 구성하는 비트들 중 어떤 것도 바꾸면 안됨
  - 정적(static) 멤버는 제외
  - C++에서 정의되어 있는 상수성이 여기에 해당
- **논리수준(Logical Constness)**
  - 객체의 한 비트도 수정할 수 없게 하는 것이 아닌, 일부 몇 비트 정도는 바꿀 수 있되, 그것을 사용자 측에서 알아채지 못하게 함

한가지, '제대로 *const*'로 동작하지 않는데도 이 비트수준 상수성 검사를 통과하는 멤버 함수들이 있을 수 있다. 어떤 포인터가 *가리키는 대상*을 수정하는 멤버 함수들 중 상당수가 이와 같은 경우이다. 그 *포인터*가 객체의 멤버로 들어 있는 한, 이 함수는 비트수준 상수성을 가지는 것으로 판별하고, 컴파일러도 에러를 발생하지 않는다.

```cpp
class CTextBook {
public:
  
  ...

  // 부적절한, 그러나 비트수준 상수성에 있어 허용되는 상수 멤버 함수
  char& operator[](std::size_t pos) const {
    return pText[pos];
  }

private:
  char *pText;
};

...

// 상수 객체를 선언
const CTextBook cctb("Hello");

// 상수 버전의 operator[]를 호출하여 cctb의 내부 데이터에 대한 포인터를 획득
char* pc = &cctb[0];

// cctb의 값이 변경되어 이제 "Jello" 라는 값을 가짐
*pc = 'J';
```

논리적 상수성에 대한 예시는 아래와 같다.

```cpp
claclass CTextBook {
public:
  
  ...

  std::size_t length() const {
    if(!lengthValid) {
      // 에러, 상수 멤버 함수 안에서는 멤버 변수에 대입이 불가능하다
      textLength = std::strlen(pText);
      lengthValid = true;
    }

    return textLength;
  }

private:
  char *pText;
  std::size_t textLength(); // 바로 직전에 계산한 텍스트 길이
  bool lengthValid;         // 이 길이가 유효한지
};
```

내부 데이터를 변경하지만 위와 같이 사용자에게는 숨겨저 있어 논리적 상수성을 만족한다고 하지만, 결국 C++의 컴파일러는 비트수준 상수성을 만족해야지만 에러 없이 작동한다. 즉, 위와 같은 예제는 논리적 상수성은 만족하지만 비트수준 상수정을 만족하지 못하여 에러를 발생시키게 되는 것이다.

이러한 상황일 때, 컴파일러의 비트수준 상수성을 회피하기 위해서는 *mutable* 키워드를 사용한다. *mutable*은 비정적 데이터를 비트수준 상수성의 족쇄에서 풀어 주는 효과를 보여준다. 

```cpp
class CTextBook {
public:
  
  ...

  // 부적절한, 그러나 비트수준 상수성에 있어 허용되는 상수 멤버 함수
  char& operator[](std::size_t pos) const {
    return pText[pos];
  }

private:
  char *pText;
};

...

// 상수 객체를 선언
const CTextBook cctb("Hello");

// 상수 버전의 operator[]를 호출하여 cctb의 내부 데이터에 대한 포인터를 획득
char* pc = &cctb[0];

// cctb의 값이 변경되어 이제 "Jello" 라는 값을 가짐
*pc = 'J';
```

논리적 상수성에 대한 예시는 아래와 같다.

```cpp
claclass CTextBook {
public:
  
  ...

  std::size_t length() const {
    if(!lengthValid) {
      // mutable로 선언된 멤버변수 이므로 문제 없다
      textLength = std::strlen(pText);
      lengthValid = true;
    }

    return textLength;
  }

private:
  char *pText;
  
  // 이 멤버변수들은 어떤 순간에도 수정이 가능해진다.
  // 심지어 상수 멤버 함수안에서도 수정이 가능하다.
  mutable std::size_t textLength();
  mutable bool lengthValid;
};
```

### 상수 멤버 및 비상수 멤버 함수에서 코드 중복 현상을 피하는 방법

```cpp
class TextBook {
public:
  ...

  // 상수 버전의 operator[]
  const char& operator[](std::size_t pos) const {
      // 경계 검사, 접근 데이터 로깅, 자료 무결성 검증 등

      ...

      return text[pos];
  }

  // 비상수 버전의 operator[]
  char& operator[](std::size_t pos){
      // 경계 검사, 접근 데이터 로깅, 자료 무결성 검증 등

      ...

      return text[pos];
  }

private:
  std::string text;
};
```

위와 같은 상황일때, 두 멤버 함수의 차이는 상수 멤버인가, 비상수 멤버 인가 라는 점만 다를뿐 그 내용을 동일하다. 즉, 두 함수 사이에는 그 내용이 동일 하기 때문에 코드의 중복 문제가 발생하게 되는 것이다.

이러한 코드 중복을 피하기 위해, 비상수 멤버 함수에서 캐스팅을 통해 상수 멤버 함수를 호출하도록 하는 방법이 있다.

```cpp
class TextBook {
public:
  ...

  // 이전과 동일
  const char& operator[](std::size_t pos) const {
      // 경계 검사, 접근 데이터 로깅, 자료 무결성 검증 등

      ...

      return text[pos];
  }

  // 상수 버전의 operator[]를 호출
  char& operator[](std::size_t pos){

      return const_cast<char&>(       // operator[]의 반환타입에 캐스팅 적용하여,
                                      // const를 떼어냄

        static_cast<const TextBook&>  // *this의 타입에 const를 붙임
         (*this)[pos]                 // operator[]의 상수 버전을 호출
      );
  }

private:
  std::string text;
};
```

이렇게 비상수 멤버 함수에서 상수 멤버 함수를 호출하는 것과 반대로 상수 멤버 함수에서 비상수 멤버 함수를 호출하는 것은, 위험을 초래할 수 있다. 상수 멤버 함수는 해당 객체의 논리적인 상태를 바꾸지 않겠다고 컴파일러와 약속한 함수인 반면, 비상수 멤버 함수는 이런 약속을 일절 하지 않는 다. 즉, 상수 멤버에서 비상수 멤버를 호출하게 되면, 수정하지 않겠다고 약속한 그 객체를 배신하게 되고, 그 객체는 변경될 위험이 있다.

## 요점

> *const*를 붙여 선언하면 컴파일러가 사용상의 에러를 잡아내는데 도움을 준다.

> *const*는 어떤 유효번위에 있는 객체에도 붙을수 있드며, 함수 매개변수 및 반환 타입, 멤버 함수에도 붙을 수 있다.

> 컴파일러 쪽에서 보면 비트수준 상수성을 지켜야 하지만, 프로그래머는 논리적 상수성을 사용하여 프로그래밍 해야 한다.

> 상수 멤버 및 비상수 멤버가 기능적으로 서로 똑같이 구현되어 있을 경우에는 코드의 중복을 피하는 것이 좋은데, 이때 비상수 버전이 상수 버전을 호출하도록 구현해야 한다.

---
---
*참고*. Effective C++ 3/E - Scott Meyers

---
