---
title: Design Patterns - Singleton
date: 2020-04-13 21:44:00
categories:
- Design Patterns
tags:
- Design Patterns
- Creational Patterns
- Singleton
- C++
---

# Creational Patterns - Singleton

## Intent

---

오직 한개의 클래스 인스턴스만을 갖도록 보장하고, 이에 대한 전역적인 접근점을 제공한다.

## Utility

---

- 클래스의 인스턴스가 오직 하나여야 함을 보장하고, 잘 정의된 접근점으로 모든 사용자가 접근할 수 있도록 할 때
- 유일한 인스턴스가 서브클래싱으로 확장되어야 하며, 사용자는 코드의 수정없이 확장된 서브클래스의 인스턴스를 사용할 수 있어야 할 때

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/singl014.gif)


### Elements

- **Singleton**
  - *Instance()* 연산을 정의하여, 유일한 인스턴스로 접근할 수 있도록 한다.
  - 유일한 인스턴스를 생성하는 책임을 맡는다.

사용자는 *Singleton* 클래스에 정의된 *Instence()* 연산을 통해서 유일하게 생성된 *Singleton* 인스턴스에 접근할 수 있다.

## Feature

---

### 유일하게 존재하는 인스턴스로의 접근을 통제한다

*Singleton* 클래스 자체가 인스턴스를 캡슐화하기 때문에, 이 클래스에서 사용자가 언제, 어떻게 이 인스턴스에 접근할 수 있는지를 제어 가능하다.

### 이름 공간(name space)을 좁힌다

전역 변수를 사용하여 이름 공간을 망치는 일이 없기 때문에, 전역 변수를 정의하여 발생하는 디버깅의 어려움 등의 문제를 없앨수 있다. 

### 연산 및 표현의 정제를 허용한다

*Singleton* 클래스는 상속될 수 있기 때문에, 이 상속된 서브클래스를 통해서 새로운 인스턴스를 만들수 있다.

런타임에 필요한 클래스의 인스턴스를 사용하여 응용프로그램을 구성하는 것도 가능하다.

### 인스턴스의 갯수를 변경하기가 자유롭다

*Singleton* 클래스의 인스턴스에 접근할 수 있는 허용 범위를 결정하는 연산만 추가하면 되기 때문에, 인스턴스 생성 갯수에 대한 변경이 유연하다.

### 클래스 연산을 사용하는 것보다 훨씬 유연한 방법이다

C++에서 *Singleton Pattern*과 동일한 기능을 발휘하는 방법이 *static member function*을 활용하여, 클래스 연산을 통해 구현하는 방법이 있다. 그러나 *static member function*는 가상 함수가 아니므로, 서브클래스들이 이 연산을 오버라이드 할 수 없다. 

## Implementation

---

### Implementation Method

#### 인스턴스가 유일해야 함을 보장한다

*Singleton Pattern*은 클래스의 인스턴스가 오로지 하나임을 보장해야 한다.

C++에서 *Singleton Pattern*을 구현하기 위해선, *static member function*을 활용한다.  

*Singleton* 클래스에 *Instance()* 연산을 *static member function*으로 정의하여 클래스 연산을 정의한다.  
*Singleton* 클래스는 또한 *static member variable*로 *m_instance*를 정의하여 실제로 생성될 유일한 인스턴스에 대한 참조자를 가지도록 한다.

```cpp
class Singleton {
public:
  static Singleton* Instance() {
    if(m_instance == 0){
      // 처음 접근할시, 새로운 인스턴스 생성
      m_instance = new Singleton();
    }

    return m_instance;
  }

protected:
  // 생성자를 protected로 선언하여
  // Instance() 이외의 접근을 막음
  Singleton();

private:
  static Singleton* m_instance;
};

Singleton* Singleton::m_instance = 0;
```

C++를 사용한 구현에서 주의해야 할 것은, *Singleton*을 전역 변수나 정적 객체로 정의하고 이를 자동 초기화하는 것만으로는 충분하지 않다는 것이다. 그 이유는 아래와 같다.

- 정적 객체의 유일한 인스턴스만 선언되리라는 보장을 할 수 없다.
  - 정적 객체에서 인스턴스를 얻는 선언문이 프로그램 다른 부분에 존재하더라도 이를 확인하고 방지할 방법이 없다.

- 정적 초기화 시점에 모든 *Singleton*을 인스턴스화하기 위해 필요한 정보가 없을 수도 있다.
  - *Singleton*은 프로그램 실행 중간에 계산으로 자신의 값이 결정될 수 있다. 그러나 정적으로 선언된 객체라면 그 내부에 정의된 변수의 값을 처음에만 정의할 수 있지, 나중에 다시 변결할 수 없다.

- C++에서는 전역 객체에 대한 생성자를 언제 호출하는지에 대한 명확한 순서를 정의하지 않는다.

또한 전역 객체나 정적 객체의 또 다른 문제점은 모든 *Singleton*의 사용 여부와 상관없이 일단 모두 생성된다는 점이다. 그러나 *static member function* 기법을 이용하면 실제 호출이 일어나는 시점에서 객체를 생성하는 것이 가능하다.

#### Singleton 클래스를 서브클래싱 한다

핵심은 *Singleton*의 인스턴스를 참조하는 변수가 이들 서브클래스의 인스턴스로 초기화 되어야 한다는 것이다.

가장 쉬운 방법은 *Singleton* 클래스의 *Instance()* 연산을 사용할 때 어떤 *Singleton*을 사용할지 결정하는 것으로, 수퍼클래스의 *Singleton*을 사용할지, 서브클래스의 *Singleton*을 사용할지를 결정한다.

두번째 방법은 *Instance()* 연산의 구현을 슈퍼클래스가 아닌 서브클래스에서 하는 것이다. 이는 *Singleton*의 구현 클래스를 연결 시점에 결정할 수 있게 대준다.

좀더 유연한 방법은 ***Singleton*에 대한 레지스트리**를 사용하는 것이다. *Instance()* 연산에 가능한 *Singleton* 클래스 클래스 집합을 정의하는 대신에 *Singleton* 클래스는 이 *Singleton* 인스턴스를 레지스트리에 이름을 가지는 인스턴스로 등록한다.

```cpp
class Singleton {
public:
  static void Register(const char* name, Singlton*);
  
  static Singlton* Instance() {
    if(m_instance == 0){
      // 사용자 또는 환경변수는 시작 시에 이것을 지원한다
      const char* singletonName = getenv("SINGLETON");
      
      // 해당하는 Singleton이 없다면 0을 반환
      m_instance = Lookup(singletonName);
    }

    return m_instance;
  }

protected:
  static Singlton* Lookup(const char* name);

private:
  static Singlton* m_instance;
  static std::List<NameSingletonPair>* m_registry;
};
```

*Singleton* 클래스가 자신을 어디에 등록할지에 대한 방법은 여러 방법이 있다.  

그중 한가지 방법은 생성자 이다. 예를 들어, *MySingleton*이라는 서브클래스는 다음과 같이 구현이 가능하다.

```cpp
MySingleton::MySingleton() {
  ...
  
  Singleton::Register("MySingleton", this);
  
  ...
}
```

생성자는 누군가가 자신을 생성하지 않는 한 호출되지 않는 연산이기 때문에, 이 문제를 해결하기 위해 *MySingleton* 인스턴스를 정적 객체로 정의한다.

### Implementation Example

```cpp
/**
* @brief SuperClass Singleton - Display
*/
class Singleton {
public:
  virtual ~Singleton() {}

  Singleton(const Singleton&) = delete;
  Singleton& operator= (const Singleton) = delete;

  static Singleton* getInstance() {
    if(m_instance == nullptr){
      m_instance = new Singleton();
    }

    return m_instance;
  }

  void printInfo() {}
  
protected:
  Singleton();

private:
  static Singleton* m_instance;
};

Singleton* Singleton::m_instance = 0;

...

  Singleton::getInstance()->printInfo();

...

```

## Related Pattern

---

*Abstract Factory Pattern*, *Builder Pattern*, *Prototype Pattern*등 많은 패턴들이 *Singleton Pattern*으로 구현될 수 이다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
