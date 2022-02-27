---
title: Effective C++ - Item.6
date: 2020-05-02 12:08:00
categories: [C++, Effective C++]
tags:
- C++
- Effective C++
---

# 컴파일러가 만들어낸 함수가 필요 없으면 확실히 이들의 사용을 금하자

---

## 내용

### private 멤버로 선언 하기

기본적으로 컴파일러가 생성하는 함수는 모두 공개가 되는 public 멤버가 된다. 이러한 컴파일러가 생성하는 함수가 저절로 만들어지는 것을 막기위해 프로그래머가 직접 선언해야 한다는 점은 맞지만, 이들을 public 멤버로 선언해야 한다는 것은 아니다. 

이러한 함수들을 public이 아닌 private 멤버로 선언하도록 한다면, 일단 클래스 멤버 함수가 명시적으로 선언되기 때문에, 컴파일러는 자신의 시본 버전을 만들 수 없게 된다. 또란 이 함수들은 private의 접근성을 가지므로 외부로부터의 호출을 차단할 수 있다.

### 정의(define) 하지 않기

private로 명시적으로 선언된다 하더라도, 그 클래스의 멤버 및 friend 함수가 호출할 수 있다는 허점이 존재한다. 이 허점마저 극복하기 위해서, 해당 함수의 *'정의(define)'* 자체를 하지 않는 방법이 있다.

정의되지 않은 함수를 누군가가 호출하려 했다면, 분명히 링크 시점에 에러를 발생시킬 것이다.

```cpp
class HomeForSales {
 public:
    
  ...

 private:
    
  ...

  // 선언만 한다
  HomeForSales(const HomeForSales&);
  HomeForSales& operator=(const HomeForSales&);
};
```

사용자가 HomeForSales 객체의 복사를 시도하려고 하면, 컴파일러에서 에러를 낼 것이고, 멤버 함수 및 friend 함수안에서 호출한다면 링커에서 에러를 낼 것이다.

추가적으로 링크 시점의 에러를 컴파일 시점의 에러로 옮길 수 있다. 이는 에러 탐지를 나중에 미루는 것보다 미리 아는 것이 좋기 때문에 유용하다. 이 방법은 복사 생성자와 복사 대입 연산자를 private로 선언하되, 이것을 HomeForSales 자체에 넣지 말고 별도의 기본 클래스에 넣고 이것으로부터 HoomeForSales를 파생시키는 것이다. 그리고 그 별도의 기본 클래스는 복사 방지만 맡는다는 특별한 의미를 부여한다.

```cpp
class UnCopyable {
 protected:
  // 파생된 객체에 대해서 생성과 소멸을 허용
  UnCopyable() {}
  ~UnCopyable() {}

 private:
  // 복사에 대해 방지
  UnCopyable(const UnCopyable&);
  UnCopyable& operator=(const UnCopyable&);
};

// 기본 클래스를 private로 상속함으로써
// 복사 생성자도, 복사 대입 연산자도 이제는 선언되지 않는다
class HomeForSales : private UnCopyable {

  ...

};
```

### 복사 방지 기본클래스의 구현와 사용법

위의 예의 복사 방지를 위한 기본 클래스(UnCopyable)의 구현과 사용법에 대하여 몇가지 중점이 있다.

UnCopyable로부터의 상속은 public일 필요가 없다. 그리고 UnCopyable의 소멸자는 가상 소멸자가 아니여도 된다. 또한 UnCopyable 클래스는 데이터 멤버가 전혀 없기 때문에 추후 Item.39에서 공부하게될 *공백 기본 클래스 최적화(Empty Base Class Optimization)* 기법이 적용될 여지가 있지만, UnCopyable 클래스는 기본 클래스이기 때문에 이 기법을 사용하게 되면 다중 상속으로 갈 가능성이 있다. 그렇게 되면 다중 상속이 문제가 되는데, 다중 상속 시에는 공백 기본 클래스 최적화가 돌아가지 못할 때가 종종 존재한다.

## 요점

> 컴파일러에서 자동으로 제공하는 기능을 허용하지 않으려면, 대응되는 멤버 함수를 private로 선언한 후에 구현은 하지 않은 채로 둔다.

> UnCopyable과 같이 복사 방지 기본 클래스를 사용하는것도 좋은 방법이다.

---
---
*참고*. Effective C++ 3/E - Scott Meyers

---
