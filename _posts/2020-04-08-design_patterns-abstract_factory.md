---
title: Design Patterns - Abstract Factory
date: 2020-04-08 21:16:00
categories:
- Design Patterns
tags:
- Design Patterns
- Creational Patterns
- Abstract Factory
- C++
---

# Creational Patterns - Abstract Factory

## Intent

상세화된 서브클래스를 정의하지 않고도 서로 관련성이 있거나 독립적인 여러 객체의 군을 생성하기 위한 인터페이스를 제공

## Utility

- 객체가 생성되거나 구성, 표현되는 방식과 무관하게 시스템을 독립적으로 만들고자 할때
- 여러 제품들중 하나를 선택해서 시스템을 설정해야하고 한번 구성한 제품을 다른것으로 대체할 수 있을때
- 관련된 제품 객체들이 함께 사용되도록 설계되었고, 이 부분에 대한 제약이 외부에도 지켜지도록 하고 싶을때
- 제품에 대한 클래스 라이브러리를 제공하고, 그들의 구현이 아닌 인터페이스를 노출시키도 싶을때

## Structure
![example](https://online.visual-paradigm.com/repository/images/eff87ae6-0414-4e79-ada5-90eaf76a3fbe.png)

### Elements

- **AbstractFactory**
  - 개념적 제품에 대한 객체를 생성하는 연산으로 인터페이스를 정의
- **ConcreteFactory**
  - 구체적인 제품에 대한 객체를 생성하는 연산을 구현
- **AbstractProduct**
  - 개념적 제품 객체에 대한 인터페이스를 정의
- **ConcreteProduct(Product)**
  - 구체적으로 팩토리가 생성할 객체를 정의하고, *AbstractProduct* 가 정의하는 인터페이스를 구현
- **Client**
  - *AbstractFactory* 와 *AbstractProduct* 클레스에 선언된 인터페이스를 사용
  
## Feature

### 구체적인 클래스를 분리
- 제품 객체를 생성하는 과정과 책임을 캡슐화 하여 구체적인 구현 클래스가 사용자에게서 분리한다.
- 제품군을 쉽게 대체 가능
  - 일반적으로 *ConcreteFactory* 의 클래스는 응용프로그램에서 한번만 나타나기 때문에 응용프로그램이 사용할 *ConcreteFactory* 의 변경이 용이하다.
  - *AbstractFactory* 는 필요한 모든 것을 생성하기 때문에 전체 제품군은 한번에 변경이 가능하다.
- 제품 사이의 일관성 증가
  - 하나의 군 안에 속한 제품 객체들이 함께 동작하도록 설계되어 있을때, 응용 프로그램은 한 번에 오직 한 군에서 만든 객체를 사용하도록 하여 프로그램의 일관성 증가한다.
- 새로운 종류의 제품을 제공하기가 어려움
  - 생산되는 제품은 *AbstractFactory* 가 생성할수 있는 제품 집합에만 고정되어 있다.
  - 새로운 제품을 생산하기 위해선 *Factory* 의 구현을 변경해야 한다.

## Implementation

```cpp
////////////////////////////////////////////////////////////////////////////////
// AbstractProduct
////////////////////////////////////////////////////////////////////////////////
class Button{
public:
  virtual ~Button() {}

  virtual void paint() = 0;
};

class MousePointer{
public:
  virtual ~MousePointer() {}
  
  virtual void paint() = 0;
};

////////////////////////////////////////////////////////////////////////////////
// ConcreteProduct(Product)
////////////////////////////////////////////////////////////////////////////////
class WinButton : public Button{
public:
  void paint() override { std::cout << "WinButton" << std::endl }
};

class WinMousePointer : public MousePointer{
public:
  void paint() override { std::cout << "WinMousePointer" << std::endl }
};

class OSXButton : public Button{
public:
  void paint() override { std::cout << "OSXButton" << std::endl }
};

class OSXMousePointer : public MousePointer{
public:
  void paint() override { std::cout << "OSXMousePointer" << std::endl }
};

////////////////////////////////////////////////////////////////////////////////
// AbstractFactory
////////////////////////////////////////////////////////////////////////////////
class GUIFactory{
public:
  virtual ~GUIFactory() {}

  virtual Button* createButton() = 0;
  virtual MousePointer* createMousePointer() = 0;
};

////////////////////////////////////////////////////////////////////////////////
// ConcreteFactory
////////////////////////////////////////////////////////////////////////////////
class WinGUIFactory : public GUIFactory{
public:
    Button* createButton() override { return new WinButton() };
    MousePointer* createMousePointer() override { return new WinMousePointer() };
};

class OSXGUIFactory : public GUIFactory{
public:
    Button* createButton() override { return new OSXButton() };
    MousePointer* createMousePointer() override { return new OSXMousePointer() };
};

...

  // 상황에 맞춰 ConcreteFactory을 통해 Factory 객체 생성
  GUIFactory* factory = nullptr;

  if(isWindow())
    factory = new WinGUIFactory();
  else
    factory = new OSXGUIFactory();

  // Factory에서 실제 Product 객체 생성
  Button* btn = factory->createButton();
  MousePointer* pointer = factory->createMousePointer();

...

```

## Related Pattern

- **Factory Method Pattern**
- **Singletern Pattern**

---
---
*참고*. Professional C++ 4/E - Marc Gregoire

---
