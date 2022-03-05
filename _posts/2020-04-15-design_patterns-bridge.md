---
title: Design Patterns - Bridge
date: 2020-04-15 11:20:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns
- Bridge
- C++
---

# Structural Patterns - Bridge

## Intent

---

구현에서 추상을 분리하여, 이들이 독립적으로 다야엉을 가질 수 있도록 한다.

핸들(Hanle), 구현부(Body) 라고도 불린다.

## Utility

---

- 추상적 새념과 이에 대한 구현 사잉의 지속적인 종속 관계를 피하고 싶을 때
- 런타일에 구현 방법을 선택하거나 구현 내용을 변경하고 싶을 떄
- 추상적 개념과 구현 모두가 독립적으로 서브클래싱을 통해 확장되어야 할 떄
- 추상적 개념에 대한 구현 내용을 변경하는 것이 다른 관련 프로그램에 아무런 영향을 주지 않을 때
- 클래스 계통에서 클래스의 수가 급증하는 것을 방지하고자 할 떄
- 여러 객체들에 걸쳐 구현을 공유하고자 하며, 이러한 사실을 사용자에게 공개하고 싶지 않을 때
- *(C++에서 해당)* 사용자들은 사용자에게 구현을 완벽하게 은닉하고자 할 때

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/bridge.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/bridg100.gif)

### Elements

- **Abstraction**
  - *window*에 해당
  - 추상적 개념에 대한 인터페이스를 제공
  - 객체 구현자(*Implmentor*)에 대한 참조자를 관리
- **RefinedAbstraction**
  - *IconWindw*에 해당
  - 추상적 개념에 정의된 인터페이스를 확장
- **Implementor**
  - *WindowImp*에 해당
  - 추현 클래스에 대한 인터페이스를 제공
  - 실질적인 구현을 제공한 서브클래스들의 공통적인 연산의 시그네처만을 정의
  - 일반적으로 *Implementor* 인터페이스는 기본적인 구현 연산을 수행하고, *Abstraction*은 더 추상화된 서비스 관점의 인터페이스를 제공
- **ConcreteImplementor**
  - *XWindowImp*, *PMWindowImp*에 해당
  - *Implementor* 인터페이스를 구현하여 실질적인 구현 내용 제공

*Abstraction* 클래스가 사용자 요청을 *Implementor* 객체에게 전달한다.

## Feature

---

### 인터페이스과 구현을 분리

구현이 인터페이스에 얽매이지 않게 되면서, 추상적 개념에 대한 어떤 방식의 구현을 선택할 것인지 런타임에 결정 가능해 진다. 이는 런타임에 어떤 객체가 자신의 구현을 수시로 변경할 수 있음을 의미한다.

*Abstraction*과 *Implementor*의 분리는 컴파일 타임 의존성을 제거할 수 있다. 구현을 변경하더라도 추상적 개념에 대한 클래스를 다시 컴파일할 필요가 없으며, 추상적 개념 클래스과 관련된 다른 코드 역시 다시 컴파일할 필요가 없다.

시스템 구조의 계층화(Layering)이 가능해진다. 이러한 시스템의 상위 수준 영역에서는 *Abstraction*과 *Implementor*만 알면 된다.

### 확장성 제고

*Abstraction*과 *Implementor*를 독립적으로 확장 가능하다.

### 구현 세부사항 은닉

상세한 구현 내용응 사용자에게서 은닉 시킬수 있다.

## Implementation

---

### Considerations point in implementaion

#### *Implementor* 하나만 둔다

구현방법이 오직 하나뿐 일때 *Implementor*를 추상 클래스로 정의하는 것은 불필요하다. 이는 개념의 추상화와 이를 구현하는 구현 사이에 일대일 관계가 성립되므로써, *Bridge Pattern*의 의미를 퇴색시킨다.

그러나, 그럼에도 추상적 개념과 구현부를 분리하는 것은 여전히 의미가 있는데, 이는 사용자를 클래스 구현의 변경에서 독립시킬수 있기 때문이다. 즉, 클래스의 구현이 변경되었다 하더라도 사용자를 다시 컴파일할 필요는 없고, 단지 링크 과정만 재수행 하면 된다.

C++에서는 굳이 이를 두 개의 파일로 구분할 필요 없이 *Implementor* 클래스의 인터페이스를 사용자에게 보이지 않도록 private영역에 정의하면 된다. 이로써 사용자는 private에 정의된 인터페이스 구현에 접근할 수 없게 된다.

#### 정확한 *Implementor* 객체를 생성

*Implemetor* 클래스가 여러개일때, 어떤 *Implementor* 클래스를 인스턴스를 언제, 어떻게, 몇개나 생성할지 결정해야 하는데, 이에 대해 아래와 같은 방법이 있다.

- *Abstraction* 클래스가 모든 *ConcreteImplementor*를 알고 있다면, 이들중 하나를 자신의 생성자 안에서 생성한다.
- 초기에는 기본 구현을 선택한 후, 필요에 따라 다른 것을 선택하도록 한다.
- 결정을 다른 객체에게 위임한다.

#### *Implementor*를 공유

**Coplien**은  C++로 구현된 *Bridge Pattern(Handle/Body)*에서 여러 객체들이 동일한 구현을 공유할수 있는 방법을 제시한다.

참조 카운트를 이용하여, 이 카운트를 증가시키고 감소시키면서 참조 횟수가 0이 되면 객체를 자동으로 지우도록 한다.

```cpp
Handle& Handle::operator= (const Handle& rhs) {
  rhs._body->Ref();
  _body->UnRef();

  if(_body->RefCount() == 0)
    delete _body;

  _body = rhs._body;

  return *this;
}
```

#### 다중상속을 이용

C++에서는 다중상속을 이용하여 인터페이스와 구현을 합칠수 있다.

그러나 이런방법은 코드의 상속에 기반을 둔 것이디 때문에, 인터페이스에 대한 구현을 영구히 종속시켜 버린다. 따라서 이러한 다중상속을 이용한 구현은, *Bridge Pattern*의 의미를 퇴색 시킨다.

### Implement Example

```cpp
/**
* @brief Abstraction - Window
* @details 추상적 개념의 인터페이스를 제공
*/
class Window(){
public:
  virtual ~Window() {}
  
  // Window가 처리할 요청
  virtual void getContents() = 0;
  
  // 구현 클래스를 이용하여 처리할 요청
  virtual void displayWindow() {
    Redner* render = getRenderImpl();
    render->renderWindow();
  }

protected:
  // Implementor 객체 생성 및 관리
  Render* getRenderImpl() {
    if(!m_impl){

#ifdef OS_WINDOW
      m_impl = dynamic_cast<Render*>(new WinRender());
#else
      m_impl = dynamic_cast<Render*>(new OSXRender());

    }

    return m_impl;
  }

private:
  Render* m_impl == nullptr;
};

/**
* @brief RefinedAbstraction - ImageWindow
* @details 추상적 개념의 인터페이스를 확장
*          다른 종류의 window 정의하여 추가적인 서비스를 제공
*/
class ImageWindow() : public Window {
public:
  void getContents() override { std::cout << "get Image File" << std::endl; }
};

/**
* @brief RefinedAbstraction - VideoWindow
* @details 추상적 개념의 인터페이스를 확장
*          다른 종류의 window 정의하여 추가적인 서비스를 제공
*/
class VideoWindow() : public Window {
public:
  void getContents() override { std::cout << "get Video File" << std::endl; }
};

/**
* @brief Implementor - Render
* @details Abstraction의 세부 행위의 인터페이스 정의
*/
class Render() {
public:
  virtual ~Render() {}
  virtual void renderWindow() = 0;
};

/**
* @brief ConcreteImplementor - WinRender
* @details Implementor를 상속받아 Abstraction의 세부 행위의 인터페이스를 구현
*/
class WinRender() : public Render {
public:
  void renderWindow() override {
    std::cout << "[WINDOW] window rendering " << std::endl;
  }
};

/**
* @brief ConcreteImplementor - OSXRender
* @details Implementor를 상속받아 Abstraction의 세부 행위의 인터페이스를 구현
*/
class OSXRender() : public Render {
public:
  void renderWindow() override {
    std::cout << "[OSX] window rendering " << std::endl;
  }
};

...

#define OS_WINDOW 1
  
  //RefinedAbstraction인 ImageWindow 객체 생성
  ImageWindow imgWindow;
  VideoWindow videoWindow;

  // Abstraction에서 확장된 각자 자신만의 서비스 구현체 실행
  imgWindow.getContents();
  videoWindow.getContents();

  // 상황에 맞게 선택된 Implementor 실행
  imgWindow.displayWindow();
  videoWindow.displayWindow();

...

```

## Related Pattern

---

*Abstract Factory Pattern*은 특정 *Bridge*를 생성하고 복합할 수 있도록 해준다.

*Adapter Pattern*은 서로 관련이 없는 클래스들이 함께 동작하게 만드는 쪽에 특화시켜 만들어진 패턴이다. 이 패턴은 대게 각 클래스의 설계가 끝난 이후에 적용된다.  
*Bridge Pattern*은 설계 단계 초기에 투입되어 추상화 및 구현이 독립적으로 다양화되도록 만드는데 쓰인다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
