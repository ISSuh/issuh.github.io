---
title: Design Patterns - Factory Method
date: 2020-04-11 12:04:00
categories:
- Design Patterns
tags:
- Design Patterns
- Creational Patterns
- Factory Method
- C++
---

# Creational Patterns - Factory Method

## Intent

---

객페를 생성하기 위해 인터페이스를 정의하지만, 어떤 클래스의 인스턴스를 생성할지에 대한 결정은 서브클래스가 하도록 한다.

때때로 *Factory Method*는 *Virtual Constructor*라고도 불린다.

## Utility

---

- 어떤 클래스가 자신이 생성해야 하는 객체의 클래스를 예측할 수 없을 떄
- 생성할 객체를 기술하는 책임을 자신의 서브클래스가 지정했으면 할 떄
- 객체 생성의 책임을 몇개의 보조 서브클래스 가운데 하나에게 위임하고, 어떤 서브클래스가 위임자인지에 대한 정보를 최대한 숨기고 싶을 떄

## Structure

---

![example](https://online.visual-paradigm.com/repository/images/7b40f65b-07b8-4758-8d64-ed22a0f4fddf.png)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/fmeth049.gif)

### Elements

- **Creator(Application)**
  - *Product* 객체 생성을 위해 *Factory Method*를 호출
  - *Product* 타입의 객체를 반환하는 *Factory Method*를 선언
  - *Creator* 클래스는 *Factory Method*를 기본적으로 구현하고, 이 구현에서는 *ConcreteProduct* 객체를 반환
- **ConcreteCreator(MyApplication)**
  - *Factory Method*를 재정의하여 *ConcreteProduct* 인스턴스를 반환
- **Product(Document)**
  - *Factory Method*가 생성하는 객체의 인터페이스를 정의
- **ConcreteProduct(MyDocument)**
  - *Product* 클래스에 정의된 인터페이스를 구현

*Creator*는 자신의 서브클래스를 통해 실제 필요한 *Factory Method*를 정의하여 적절한 *ConcreteProduct*의 인스턴스를 반환할 수 있게 한다.

## Feature

---

- *Factory Method Pattern*은 응용프로그램에 국한된 클래스가 코드에 종속되지 않도록 한다.
  - 응용프로그램은 *Product* 클래스에 정의된 인터페이스만 동작하도록 코드를 작성기 때문에 사용자가 정의한 어떠한 *ConcreteProduct* 클래스와도 동작할수 있게 한다.
- *ConcreteProduct* 객체 하나만 생성하려 할때도 *Creator* 클래스를 상속하여 서브클래스를 만들어야 한다.
- 서브클래스에 대한 *훅 메서드(Hook Method)* 를 제공한다.
  - 객체를 직접 생성하는 것보다 *Factort Method*로 클래스 내부에서 객체를 생성하여 응용성을 높힌다.
  - *Factort Method Pattern*에서는 객체별로 서로 다른 버전을 제공하는 훅 기능을 서브클래스에 정의한다.
- **병렬적인 클래스 계통(Connects parallel class hierarchies)** 을 연결하는 역할을 담당한다.
  - *병렬적 클래스 계통*은 클래스가 자신의 책임을 분리된 다른 클래스에 위임할때 발생한다.
  - 사용자와 상호작용을 구현하고 특정한 조작에 관련된 상태를 추적, 저장하는 별도의 *Manipulator* 객체를 사용하고, 서로 다른 *Manipulator* 클래스의 서브클래스를 사용하여 특정한 상호작용을 처리하도록 한다.

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/fmeth048.gif)

## Implementation

---

*Factort Method Pattern*을 구현할떄는 다음과 같은 사항을 고려해야 한다.

### 구현방법은 크게 두가지이다

1. *Creator* 클래스를 추상 클래스로 정의하고, 정의한 *Factory Method*에 대한 구현은 제공하지 않는다.
2. *Creator*가 구체 클래스 이고, *Factory Method*에 대한 기본 구현을 제공한다.

### *Factory Method*를 매개변수화 한다

*Factory Method*갸 매개변수로 받아서 어떤 종류의 제품을 생성할지 식별하게 한다.
이때 생성하는 모든 객체는 *Product*라는 인터페이스를 모두 만족해야 한다.

매개변수회된 *Factory Method*는 다음과 같은 일반적인 형태를 가진다.

```cpp
class Creator {
public:
  virtual Product* Create(ProductId id) {
    // 나머지 Product에 대해서도 이렇게 반복한다.
    if(id == MINE)  return new MyProduct;
    if(id == YOURS) return new YourProduct;

    ...

    return 0;
  }
};
```

매개변수회된 *Factory Method*를 오버라이드 하면, Creator 클래스가 생성하는 제품을 쉽게 확장하거나 변경할수 있다.

```cpp
class MyCreator : public Creator {
public:
  Product* Create(ProductId id) override {
    // YOURS와 MINE을 바꿀떄
    if(id == MINE)  return new YourProduct;
    if(id == YOURS) return new MyProduct;

    if(id == THEIRS)  return new TheirProduct;

    // MINE, YOURS, THEIRS 만 처리되도록 하였으므로,
    // 이외의 것을 처리하기 위해 부모클래스에게 요청
    return Creator::Create(id);
  }
};
```

### 템플릿을 사용하여 서브클래싱을 피한다

*Factory Method*를 쓰면 생기는 잠재적인 문제중 하나는 그냥 *Product* 클래스 하나를 추가 할 때마다 서브클래싱을 해야 한다는 점이다. 이를 해결하기 위해 *Creator* 클래스의 서브클래스가 되는 *템플릿 클래스*정의하고 이것이 *Product* 클래스로 매개변수화되도록 만드는 것이다.

```cpp
class Creator {
public:
  virtual Product* CreateProduct() = 0;
}

template<class TheProduct>
class StandardCreator : public Creator {
public:
  // Creator를 상속받는 *Factory Method*를 구현할
  // 서브클래스를 템플릿 클래스로 정의
  Product* CreateProduct() override { return new TheProduct; };
};

class MyProduct : public Product {
public:
  MyProduct();
  ...

};

...

  StandardCreator<MyProduct> myCreator;

...
```

### Implementation Example

```cpp
/**
* @brief Product(AbstractProduct) - Display
*/
class Display {
public:
  virtual ~Button() {}
  virtual void show() const = 0;
};

/**
* @brief ConcreteProduct - WinDisplay
*/
class WinDisplay : public Display {
public:
  void show() const override { std::cout << "WIN" << std::endl; }
};

/**
* @brief ConcreteProduct - OSXDisplay
*/
class OSXDisplay : public Display {
public:
  void show() const override { std::cout << "OSX" << std::endl; }
};

/**
* @brief Creator(AbstractCreator)
*/
class DisplayCreator {
public:
  virtual ~Creator() {}

  // Factory Method
  virtual Display* CreateDisplay(OSType type) const = 0;
};

/**
* @brief ConcreteCreator
*/
class MyDisplayCreator : public DisplayCreator {
public:
  // Factory Method 구현
  Display* CreateDisplay(OSType type) const override {
    switch(type){
      case OS_WIN: { 
        return new WinDisplay();
      }
      case OS_OSX: {
        return new OSXDisplay();
      }
      default: {
        return new Display();
      }
    }
  }
};

...

  MyDisplayCreator displayCreator;
  Display* display = dynamic_cast<WinDisplay>(displayCreator.CreateDisplay(OS_WIN));

  display->show();

...
```

## Related Pattern

---

*Abstract Factory Pattern*은 이 *Factory Method*를 이용해서 구현할 때가 많다.

*Factory Method*는 *Template Method Pattern* 에서 사용될 떄가 많다.

*Prototype Pattern*은 *Creator* 클래스의 상속이 필요하지 않다. 그러나 *Product* 클래스에 정의된 초기화 연산은 필요하다. *Creator* 클래스는 객체의 초기화를 위해 초기화 연산을 사용하지만, *Factory Method*는 이러한 연산이 필요하지 않다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
