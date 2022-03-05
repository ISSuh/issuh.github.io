---
title: Design Patterns - Template Method
date: 2020-04-27 21:32:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Template Method
- C++
---

# Behavioral Patterns - Template Method

## Intent

---

객체의 연산에는 알고리즘의 뼈대만을 정의하고 각 단계에서 수행할 구체적 처리는 서브클래스 쪽으로 미룬다.

알고리즘의 구조자페는 그대로 놔둔채 알고리즘 각 단계 처리를 서브클래스에서 재정의할 수 있게 한다.

## Utility

---

- 어떤 한 알고리즘을 이루는 부분 중 변하지 않는 부분을 한 번 정의해 놓고 다양해질 수 있는 부분은 서브클래스에서 정의할 수 있도록 남겨두고자 할 떄
- 서브클래스 사이의 공통적인 행동을 추출하여 하나의 공통 클래스에 몰아둠으로써 코드 중복을 피하고 싶을 떄
  - 기존 코드에서 나타나는 차이점을 뽑아 이를 별도의 새로운 연산들로 구분한 뒤, 달라진 코드 부분을 이 새로운 연산을 호출하는 템플릿 메소드로 대체하는 것이다.
- 서브클래스의 확장을 제어할 수 있다.
  - 템플릿 메소드가 어떤 특정한 시점에 "훅(Hook)" 연산을 호출하도록 정의하여 그 특정 시점에만 확장되도록 한다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/tmethod.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/tmeth007.gif)

### Elements

- **AbstractClass**
  - *Application*에 해당
  - 서브클래스들이 재정의를 통해 구현해야 하는 알고리즘 처리 단계 내의 **기본연산**을 정의
  - 알고리즘의 뼈대를 정의하는 *Template Method*를 구현
  - *Template Method*는 *AbstractClass*에 정의된 연산 또는 다른 객체 연산뿐만 아니라 기본 연산도 호출
- **ConcreteClass**
  - *MyApplication*에 해당
  - 서브클래스마다 달라진 알고리즘 처리 단계를 수행학기 위한 기본 연산을 구현

#### Elements Communication

*ConcreteClass*는 *AbstractClass*를 통하여 알고리즘의 변하지 않는 처리 단계를 구현한다.

## Feature

---

*Template Method*는 코드 재사용을 위한 기본 기술이다. 특히, 클래스 라이브러리 구현 시, 라이브러리에 정의할 클랫스들의 공통 부분을 분리하는 수단이기 때문에 자주 사용된다.

*Template Method*는 역전된 제어 구조를 끌어낸다. 즉, 부모 클래스는 서브클래스에 정의된 연산을 호출할 수 있지만 반대 방향의 호출은 안된다.

*Template Method*는 여러 종류의 연산 중 하나를 호출한다.

- 구체 연산(*Concrete Operation*)
  - *ConcreteClass*나 사용자 클래스에 정의된 연산
- *AbstractClass* 구체 연산
  - 서브클래스에서 일반적으로 유용한 연산
- 기본 연산
  - 추상화된 연산
- *Factory Method*
- **훅 연산(*Hook Operation*)**
  - 필요하다면 서브클래스에서 확장할 수 있는 기본 행동을 제공하는 연산
  - 기본적으로는 아무 내용도 정의하지 않는다

*Template Method Pattern*에서는 어떤 연산이 오버라이드가 가능한 훅 연산인지, 꼭 오버라이드를 해야하는 추상 연산인지를 지정해 두는 것이 매우 중요하다.

훅 연산은 반드시 재정의 해야하는 연산이며 추상할 클래스를 효과적으로 재사용하기 위해서, 서브클래스 작성자는 어떤 연산들이 오버라이드용으로 설계되었는지를 명확히 이해하고 있어야 한다. 서브클래스는 부모 클래스에 정의된 연산을 명시적으로 호출하고 또 재정의함으로써 부모 클래스 연산을 확장한다.

```cpp
void DerivedClass::Operation() {
    ParentClass::Operation();

    // 확장할 내용을 추가한다

    ...

}
```

서브클래스가 부모 클래스의 행동을 확장하는 연산들을 *Template Method*로 옮겨 놓음으로써 부모 클래스에게 서브클래스의 확장을 제어할 수 있는 권한을 부여할 수 있다. 이 아이디어는 부모 클래스의 *Template Method*에서 훅 연산을 호출하도록 하는 것이다. 서브클래스는 이 훅 연산을 재정의할 수 있다.

```cpp
void ParentClass::Operation(){
  // 부모 클래스가 정의한 행동
  HookOperation();
}
```

일반적으로 *ParentClass*에 정의된 *HookOperation()* 은 아무런 행동도 정의하지 않는다.

```cpp
void ParentClass::HookOpertaion() {}
```

서브클래스에서는 행동을 확장하려고 *HookOperation()* 을 재정의한다.
```cpp
void DerivedClass::HookOperation() {
  // 상속받는 클래스가 확장항 코드

  ...

}
```

## Implementation

---

### Considerations point in implementaion

#### C++의 접근 제한 방법을 이용

C++로 구현시, *Template Method*에서 호출하는 기본 연산들을 protected 멤버로 구현한다. 이렇게 하면 이 연산들을 *Template Method*만 호출할 수 있게 된다. 오버라이드해야 하는 기본 연산은 반드시 순수 가상함수로 정의한다. *Template Method* 자체를 재정의되면 안되므로, *Template Method*는 비사강 멤버 함수로 만든다.

#### 기본 연산의 수를 최소화

*Template Method*를 구현하는 중요한 목적 중 하나는 서브클래스가 알고리즘을 실체화하기 위해 오버라이드해야 하는 기본 연산의 갯수를 줄이는 것이다.

#### 이름을 짓는 규칙 정의

재정의가 필요한 연산은 식별이 잘 되도록 접두사를 붙이는 것이 좋다.

### Implement Example

```cpp
/**
* @brief AbstractClass - AbstractClass
* @details
*/
class AbstractClass {
public:
  void TemplateMethod() const {
    this->BaseOperation1();
    this->RequiredOperations1();
    this->BaseOperation2();
    this->Hook1();
    this->RequiredOperation2();
    this->BaseOperation3();
    this->Hook2();
  }

protected:
  void BaseOperation1() const {
    std::cout << "AbstractClass says: I am doing the bulk of the work\n";
  }
  void BaseOperation2() const {
    std::cout << "AbstractClass says: But I let subclasses override some operations\n";
  }
  void BaseOperation3() const {
    std::cout << "AbstractClass says: But I am doing the bulk of the work anyway\n";
  }

  virtual void RequiredOperations1() const = 0;
  virtual void RequiredOperation2() const = 0;

  virtual void Hook1() const {}
  virtual void Hook2() const {}
};

/**
* @brief ConcreteClass - ConcreteClass1
* @details
*/
class ConcreteClass1 : public AbstractClass {
 protected:
  void RequiredOperations1() const override {
    std::cout << "ConcreteClass1 says: Implemented Operation1\n";
  }
  void RequiredOperation2() const override {
    std::cout << "ConcreteClass1 says: Implemented Operation2\n";
  }
};

/**
* @brief ConcreteClass - ConcreteClass2
* @details
*/
class ConcreteClass2 : public AbstractClass {
 protected:
  void RequiredOperations1() const override {
    std::cout << "ConcreteClass2 says: Implemented Operation1\n";
  }
  void RequiredOperation2() const override {
    std::cout << "ConcreteClass2 says: Implemented Operation2\n";
  }
  void Hook1() const override {
    std::cout << "ConcreteClass2 says: Overridden Hook1\n";
  }
};

/**
* @brief Client - ConcreteClass2
* @details
*/
void ClientCode(AbstractClass *class_) {
  // ...
  class_->TemplateMethod();
  // ...
}


...
  
  ConcreteClass1 *concreteClass1 = new ConcreteClass1;
  ClientCode(concreteClass1);

  ConcreteClass2 *concreteClass2 = new ConcreteClass2;
  ClientCode(concreteClass2);

...

```

## Related Pattern

---

*Factory Method Pattern*은 종종 *Template Method Pattern* 이라고도 한다.

*Template Method Pattern*은 상속을 이용하여 다양한 알고리즘을 만들어 낸다. 이점은 *Strategy Pattern*과도 관계가 있으며, 각 전략들은 위임을 통해 전체 알고리즘을 다양화한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
