---
title: Design Patterns - Prototype
date: 2020-04-11 15:04:00
categories:
- Design Patterns
tags:
- Design Patterns
- Creational Patterns
- Prototype
- C++
---

# Creational Patterns - Prototype

## Intent

---

원형이 되는 인스턴스를 사용하여 생성할 객체의 종류를 명시하고, 이렇게 만든 견본을 복사해서 새로운 객체를 생성한다.

## Utility

---

- 인트턴스화할 클래스를 런타임에 지정할(동적 로딩) 떄
- 제품 클래스 계통과 병렬적으로 만드는 *Factory Class* 피하고 싶을때
- 클래스의 인스턴스들이 서로 다른 상태 조합을 사용하고 있어, 그중 하나를 선택 할 떄

## Structure

---

### Basic Structure

![example](https://online.visual-paradigm.com/repository/images/0a669ff5-a4b9-4a6c-8c57-5c923f27f1d4.png)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/proto019.gif)

### Elements

- **Prototype**
  - *Graphic*에 해당
  - 자신을 복제하는 데 필요한 인터페이스를 정의
- **ConcretePrototype**
  - *Staff*, *WholeNote*, *HalfNote*에 해당
  - 자신을 복제하는 연산(인터페이스)을 구현
- **Client**
  - *GraphicTool*에 해당
  - *Prototype*에 자기 자신의 복제를 요청하여 새로운 객체를 생성

사용자는 *Prototype* 클래스에 스스로를 복제하도록 요청해야 한다.

## Feature

---

*Prototype Pattern*을 사용할 떄 가장 신경써야 하는 점은, *Prototype*의 서브클래스가 *Clone()* 연산을 구현해야 한다는 점이다. 예를들어, 패턴을 적용할 클래스가 이미 만들어진 경우 *Clone()* 연산을 추가하기가 어려워 진다. 또한 복사를 지원하지 않거나 **환형 참조(Circular Reference)** 가 없는 객체를 포함한다면 *Clone()* 연산의 구현은 매우 어려워 지기 때문에 *Prototype Pattern* 의 적용이 어려워 진다.

### 런타임에 새로운 제품을 추가하고 삭제할수 있다

*Prototype Pattern*을 이용하면 사용자에게 *Prototype*으로 생산되는 인스턴스를 등록하는 것만으로도 시스템에 새로운 제품 클래스를 추가할 수 있게 된다.

런타임에 새로운 *Prototype*을 넣고 빼기가 쉽다는 점에서 다른 생성패턴에 비해 유연성을 지니고 있다.

#### 값들을 다양화 함으로써 새로운 객체를 명세한다

*Prototype Pattern*은 사용자와 동작할 *Prototype*에 해당하는 기존 클래스의 인스턴스를 만들어서 그 인스턴스를 등록하면, 사용자는 이 *Prototype*에 정의된 행동이 수행되어 마치 새로운 행동이 정의된 듯한 결과를 얻게 된다.

*Prototype*을 복제하는 것은 클래스의 인스턴스를 만드는 것과 동일하기 때문에 *Prototype Pattern*은 시스템에 필요한 객체를 생성하기 위해 정의할 클래스의 수를 대폭 줄여주는 효과가 있다.

### 구조를 다양화 함으로써 새로운 객체를 명세할 수 있다

세부 구성요소를 *Prototype*으로 만들어, 이것을 현재 사용 가능한 구성요소 관리자에 등록한다. 이후, 세부 구성요소를 가지는 복합객체에서 *Clone()* 연산을 구현함으로써 다른 구조를 가지는 객체의 기본 골격을 만든다.
이렇게 함으로써 복잡한 사용자 정의 구조를 사용자가 인스턴스화 하여 그 상황에 맞는 세부 구성요소를 이용할 수 있도록 한다.

### 서브클래스의 수를 줄인다

*Factory Method Pattern*를 보면 *Creator* 클래스의 계통이 제품 관련 클래스의 계통과 병렬로 복합되어 진다.

*Prototype Pattern*에서는 *Factory Method*에게 새로운 객체를 만들어 달라고 요청하는 것이 아니라 *Prototype*을 복제하는 것이므로, *Creator* 클래스에 따른 새로운 상속 계층이 필요없다.

### 동적으로 클래스에 따라 응용프로그램을 설정할 수 있다

몇몇 런타임 환경에서는 동적으로 클래스들을 응용프로그램으로 등록할 수 있도록 해준다.

동적으로 로드된 클래스의 인스턴스를 생성하고 싶은 응용프로그램은 정적으로 그 클래스의 생성자를 참조할 수 없다. 그 대신 런타임 환경이 그 클래스의 인스턴스를 자동으로 생성하고 *Prototype Manager* 에게 등록한다. 이후 응용프로그램이 이 *Prototype Manager*에게서 필요한 클래스의 인스턴스를 얻게된다.

## Implementation

---

### *Prototype Manager*를 사용한다

사용자는 *Prototype* 자체를 다루지 않고, *Prototype Manager*에서 *Prototype*을 검색하고 그것을 복제하여 사용한다.

*Prototype Manager*는 어떤 키에 부합되는 *Prototype*을 저장하고, 찾아서 반환하며, 삭제하는 기능을 담당하는 저장소이다.

### *Clone()* 연산을 구현한다

구조가 복잡한 *Prototype*을 복제할 때는 대개 **깊은 복사(Deep Copy)** 가 필요하다. 사본과 원본이 독립적일 수 있어야 하기 때문이다. 그러므로 *Clone()* 연산을 구현 할 때는 무엇을 복사하고, 무엇을 공유하도록 할 것인지 결정해야 한다.

### *Clone()* 연산을 초기화 한다

어떤 사용자는 내부 상태를 자신이 선택한 값으로 초기화하기를 바랄것이다. 일반적으로 이런 값은 *Clone()* 연산에 넘길 수 없다. 왜냐하면 *Prototype* 클래스마다 필요한 매개변수와 개수가 천차만별이기 때문이다.

*Prototype* 클래스의 상태를 설정하기 위한 연산이나, 상태를 초기화 해주는 연산을 제공해 주어야 한다. 이러한 연산은 초기화 매개변수를 인자로받아 그 복제본의 내부 상태를 설정한다. 이때 깊은 복사를 수행하는 *Clone()* 연산이 있기떄문에, 사본을 재초기화 하기 전에 사본을 삭제해야 할 수도 있다.

### Implement Example

```cpp
/**
* @brief Prototype - MousePointer
*/
class MousePointer {
public:
  virtual ~MousePointer() {}

  // Prototype의 설정, 복제를 위한 인터페이스 선언
  virtual void initailize(const Point& point) = 0;
  virtual MousePointer* clone() const = 0;

private:
  Point m_point;
};

/**
* @brief Prototype - Button
*/
class Button {
public:
  virtual ~Button() {}

  // Prototype의 설정, 복제를 위한 인터페이스 선언
  virtual void initailize(int id) = 0;
  virtual Button* clone() const = 0;

private:
  int m_id;
};

/**
* @brief ConcretePrototype - MousePointer
*/
class WINMousePointer : public MousePointer {
public:
  // 깊은 복사를 위한 복사생성자 정의
  WINMousePointer(const WINMousePointer& rhs){ 
    m_point.x = rhs.m_point.x;
    m_point.y = rhs.m_point.y;
  }

  // Prototype의 설정 인터페이스 정의
  void initailize(const Point& point) override {
    m_point.x = point.x;
    m_point.y = point.y;
  }

  // Prototype의 복제 인터페이스 정의
  MousePointer* clone() const override {
    return new WINMousePointer(*this);
  }  
};

/**
* @brief ConcretePrototype - MousePointer
*/
class OSXMousePointer : public MousePointer {
public:
  // 깊은 복사를 위한 복사생성자 정의
  OSXMousePointer(const OSXMousePointer& rhs){
    m_point.x = rhs.m_point.x;
    m_point.y = rhs.m_point.y;
  }

  // Prototype의 설정 인터페이스 정의
  void initailize(const Point& point) override {
    m_point.x = point.x;
    m_point.y = point.y;
  }

  // Prototype의 복제 인터페이스 정의
  MousePointer* clone() const override {
    return new OSXMousePointer(*this);
  }
};

/**
* @brief ConcretePrototype - Button
*/
class WINButton : public Button {
public:
  // 깊은 복사를 위한 복사생성자 정의
  WINButton(const OSXButton& rhs){ m_id = rhs.m_id; }

  // Prototype의 설정, 복제 인터페이스 정의
  void initailize(int id) override { m_id = id; }
  Button* clone() const override { return new WINButton(*this); }
};

/**
* @brief ConcretePrototype - Button
*/
class OSXButton: public Button {
public:
  // 깊은 복사를 위한 복사생성자 정의
  OSXButton(const OSXButton& rhs){ m_id = rhs.m_id; }

  // Prototype의 설정, 복제 인터페이스 정의
  void initailize(int id) override { m_id = id; }
  Button* clone() const override { return new OSXButton(*this); }
};

/**
* @brief Prototype Factory for Client
*/
class UIPrototypeFactory {
public:
  UIPrototypeFactory(OS_TYPE type){
    switch(type){
      case OS_WIN: {
        m_prototypeMousePointer = dynamic_cast<MousePointer*>(new WINMousePointer());
        m_prototypeMousePointer = dynamic_cast<Button*>(new WINButton());
        break;
      }
      case OS_OSX: {
        m_prototypeMousePointer = dynamic_cast<MousePointer*>(new OSXMousePointer());
        m_prototypeMousePointer = dynamic_cast<Button*>(new OSXButton());
        break;
      }
    }
  }

  // Prototype 기반
  virtual MousePointer* createMousePointer(const Point& point) const {
    MousePointer* mpt = m_prototypeMousePointer->clone();
    mpt->initailize(point);
    new mpt;
  }

  // Prototype 기반
  virtual Button* createButton(const int id) const {
    Button* btn = m_prototypeButton->clone();
    btn->initailize(id);
    new btn;
  }
  
private:
  MousePointer* m_prototypeMousePointer = nullptr;
  Button* m_prototypeButton = nullptr;
};

...

  UIPrototypeFactory uiFactory(OS_WIN);

  MousePointer* myMpt = uiFactory->createMousePointer({0, 0});
  Button* myBtn = uiFactory->createButton(0);

...

```

## Related Pattern

---

*Abstract Fatory Pattern*은 *Prototype*의 집합을 저장하다가 필요할 떄 복제하여 제품 객체를 반환하도록 사용 할 수도 있다.

*Prototype Pattern*은 *Composite Pattern*과 *Decorator Pattern*을 많이 사용해야 하는 설계에서 유용하게 쓰인다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
