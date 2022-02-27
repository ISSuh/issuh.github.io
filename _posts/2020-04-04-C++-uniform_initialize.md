---
title: Uniform Initialization
date: 2020-04-04 15:57:00
categories: [C++,  etc]
tags:
- C++
- Modern C++
- Uniform Initialization
---

# Uniform Initialization

## 개요

C++11 이전에는 타입의 초기화 방식이 일정하지 않았다.

```cpp
struct CircleStruct{
  int x, y;
  double radius;
};

class CircleClass{
public:
  CircleClass(int x, int y, double radius)
    : mX(x), mY(y), mRadius(radius) {}

private:
  int mX, mY;
  double mRadius;
}

...
...

  CircleStruct myCircle_1 = {10, 10, 2,.5};
  CircleClass myCircle_2(10, 10, 2.5);
```

c++11 이전에는 위와같은 struct 타입 변수와 class 타입 변수를 초기화할때, 서로 다른 방식으로 초기화를 했다.
struct에 대해서는 {...}, class 에 대해서는 (...)을 사용하여 각자 초기화를 진행한다.

그러나 c++11 이후부터 타입을 초기화 할때, {...} 문법을 사용하는 **유니폼 초기화(Uniform Initialization)** 를 사용하도록 통일시켰다.

```cpp
  CircleStruct myCircle_3 = {10, 10, 2,.5};
  CircleClass myCircle_4 = {10, 10, 2.5};

  // = 생략 가능
  CircleStruct myCircle_5{10, 10, 2,.5};
  CircleClass myCircle_6{10, 10, 2.5};
```

이러한 *유니폼 초기화* 는 구조체나 클래스 뿐만 아니라 c++에 있는 모든 대상을 초기화할때 사용할 수 있다.

```cpp
int a=3;
int b(3);
int c = {3};  // 유니폼 초기화
int d{3};     // 유니폼 초기화
int e{};      // 유니폼 초기화, e는 0이 된다.

int* pArray = new int[4]{0, 1, 2, 3};   // 동적할당 배열의 유니폼 초기화

class MyClass {
public:
  MyClass() 
    : mArray{0, 1, 2, 3} {}   // 배열형태인 클래스 멤버변수의 유니폼 초기화

private:
  int mArray[4];
};
```

*유니폼 초기화* 를 사용하면 **축소 변환(narrowing)** 을 방지할수 있다. c++ 에서는 암묵적으로 축소 변환될 때가 있다. *유니폼 초기화* 가 아닌 이전의 방식대로 초기화를 진행한다면 컴파일러에 따라 *축소 변환* 될때 경고 메세지가 발생하지만, *유니폼 초기화* 를 사용하여 초기화를 진행하면, 이를 컴파일러에서 에러 메세지가 발생하여 사전에 방지할 수 있도록 한다.

```cpp
void func(int i) { /* ... */ }

int main() {
  int x = 3.14;     // int형 변수 이므로, 3.14 에서 3 으로 값이 축소변환 된다.
  func(3.14);       // func의 매개변수가 in형 이므로,
                    // 3.14 에서 3 으로 값이 축소변환 된다.


  int y = {3.14};   // 축소로 인한 에러
  func({3.14});     // 축소로 인한 에러
}
```

## 복제 리스트 초기화와 직접 리스트 초기화

*유니폼 초기화* 는 크게 두가지 방식이 존재한다.

- **복제 리스트 초기화(Copy List Initialization)**
  - T obj = {arg1, arg2, ...};
- **직접 리스트 초기화(Direct List Initialization)**
  - T obj{arg1, arg2, ...};

c++17부터는 auto 타입 추론 기능과 관련하여 *복제 리스트 초기화* 와 *직접 리스트 초기화* 가 크게 달라졌다. c++17 이전에는 *복제 리스트 초기화* 와 *직접 리스트 초기화* 가 모두 **initializer_list<>** 로 처리됬다. c++17부터 aut는 *직접 리스트 초기화* 에 대해 값 하나만 추론한다.

```cpp
//---------------------
// c++17 이전
//---------------------

// 복제 리스트 초기화
auto a = {11};      // initializer_list<int>
auto b = {11, 22};  // initializer_list<int>

// 직접 리스트 초기화
auto c {11};        // initializer_list<int>
auto d {11, 22};    // initializer_list<int>

//---------------------
// c++17 이후
//---------------------

// 복제 리스트 초기화
auto a = {11};      // initializer_list<int>
auto b = {11, 22};  // initializer_list<int>

// 직접 리스트 초기화
auto c {11};        // int
auto d {11, 22};    // 원소가 너무 많다는 에러가 발생한다.
```

---
---
*참고*. Professional C++ 4/E - Marc Gregoire

---
