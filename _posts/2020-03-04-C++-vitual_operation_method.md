---
title: virtual 함수의 내부 작동 방식
date: 2020-03-04 15:22:00
categories: [C++,  etc]
tags:
- C++
- virtual function
---

# virtual 함수의 내부 작동 방식
---

## 정적 바인딩(Static Binding)
---

C++에서 클레스를 컴파일하면 그 클래스의 모든 함수를 담은 바이너리 객체가 생성된다. 그런데 컴파일러는 *virtual* 로 선언되지 않은 함수를 호출하는 부분을 컴파일 시간에 결정된 타입의 코드로 교체한다. 이러한 과정을 **정적 바인딩(Static Binding)** 또는 **이른 바인딩(Early Binding)** 이라 부른다.

## 가상 테이블(vtable)
---

함수를 *virtual* 로 선언하면 **vtable(가상 테이블)** 이라는 메모리 영역을 활용하여 가장 적합한 구현 코드를 호출한다.

*virtual* 함수가 하나 이상 정의된 클래스마다 *vtable* 이 하나씩 존재하며, 이 클래스로 생성한 객체마다 이 *vtable* 에 대한 포인터를 가지게 된다. 따라서 객체에 대한 멤버함수를 호출하면 *vtable* 을 보고 그 시점에서 적합한 버전의 함수를 실행하게 된다.

이러한 과정을 **동적 바인딩(Dynamic Binding)** 또는 **늦은 바인딩(Late Binding)** 이라 부른다.

```cpp
class Base {
       public:
           virtual void func1() {}
           virtual void func2() {}
           void nonVirtualFunc() {}
};
   class Derived : public Base
   {
       public:
           virtual void func2() override {}
           void nonVirtualFunc() {}
};
```

```cpp
Base myBase;
Derived myDerived;
```

위와 같은 클레스를 정의한 상태에서의 *vtable* 의 구조는 아래 그림과 같다.

![example](https://issuh.github.io/blog/assets/posts/C++/vitual_operation_method/pic_1.png)

## virtual 키워드가 필요한 이유
---

몇몇 사함들은 모든 멤버함수를 모두 *virtual* 로 선언하거나 컴파일러 차원에서 모든 멤버함수를 *virtual* 로 선언하여 동작해야 한다고 주장한다. 실제로 자바의 경우 모든 메서드를 *virtual* 메서드 취급하여 동작한다. C++에서는 왜 굳이 따로 *virtual* 키워드를 만들어서 사용할까?

현 시대의 CPU성능으로 보면 크 차이는 굉장히 미비하지만 분명히 *오버헤드* 가 발생하기에, 모든 멤버함수를 *virtual* 로 취급하는 대신 *virtual* 키워드를 두어서 개발자에게 맡기는 방향으로 진행되었다.

*virtual* 함수를 호출하려면 가장 적합한 코드를 선택하는 과정에서 포인터를 역참조해야 한다. 예를 들어 *virtual* 함수를 가진 Point 클래스로 수백만 또는 수십억개의 Point 객체를 만들어서 저장한다면 Point 객체마다 *virtual* 함수를 호출하여 엄청난 *오버헤드* 가 발생하게 된다. 또한 객체마다 *메모리 오버헤드* 도 발생한다. 함수 구현 코드 뿐만 아니라 객체마다 *vtable* 에 대한 포인터를 가져야 하기 때문에 작게나마 메모리 공간을 더 차지하게 된다.

## virtual 소멸자의 필요성
---

소멸자를 *virtual* 로 선언하지 않으면 객체가 소멸할 때 메모리가 해제되지 않을 수 있기 때문에 항상 소멸자 만큼은 클래스를 *final* 로 선언할 때를 제외하고, 항상 *virtual*로 선언을 해야한다.

예를 들어 파생 클래스의 생성자에서 동적으로 할당된 메모리를 사용하다가 소멸자에서 삭제하도록 작성했을 때, 소멸자가 호출되지 않으면 메모리가 해제되지 않는다. 마찬가지로 *std::unique_ptr* 처럼 파생 클래스에 자동적으로 삭제되는 멤버가 있을때 그 클래스의 인스턴스가 삭제될 때 소멸자가 호출되지 않으면 이런 멤버가 삭제되지 않고 남아 있게 된다.

다음과 같은 예제를 확인해보면 쉽게 이해할 수 있다.

```cpp
class Base {
public:
  Base() {}
  ~Base() {}
};

class Derived : public Base{
public:
  Derived() {
    mString = new char[30];
    cout << "mString allocated" << endl; 
  }
  
  ~Derived() {
    delete[] mString;
    cout << "mString deallocated" << endl; 
  }

private:
  char* mString;
};

int main() {
  // mString이 여기서 할당된다.
  Base* ptr = new Derived();
  
  // ~Base는 호출되지만 ~Derived는 호출되지 않는다.
  // 소멸자를 virtual로 선언하지 않았기 때문이다.
  delete ptr;
}
```

이 코드를 실행하면 Derived 객체의 소멸자가 호출되지 않는다. 

이러한 *업캐스팅* 시, 파생 클래스의 객체가 *업 캐스팅* 되기 때문에 기본 클래스로 인식이 되어 파생 클래스의 소멸자가 호출되지 않는 것이다.

이와 같은 문제를 해결하기 위해 기본 클래스의 소멸자를 *virtual* 로 선언하고, 파생 클래스에서 소멸자를 구현한다면 정상적으로 파생 클래스의 소멸자가 호출된다.

```cpp
class Base {
public:
  Base() {}
  virtual ~Base() {}
};

class Derived : public Base{
public:
  Derived() {
    mString = new char[30];
    cout << "mString allocated" << endl; 
  }
  
  virtual ~Derived() override {
    delete[] mString;
    cout << "mString deallocated" << endl; 
  }

private:
  char* mString;
};

int main() {
  Base* ptr = new Derived();
  
  delete ptr;
}
```

> NOTE : 소멸자에서 따로 처리할 일은 없고 virtual로만 지정하고 싶다면 다음과 같이 디폴트로 지정한다.

```cpp
class Base {
public:
  virtual ~Base() = default;
};
```

---
---
*참고*. Professional C++ 4/E - Marc Gregoire

---
