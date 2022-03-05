---
title: Design Patterns - Decorator
date: 2020-04-18 11:30:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns
- Decorator
- C++
---

# Structural Patterns - Decorator

## Intent

---

객체에 동적으로 새로운 책임을 추가할 수 있게 한다.

기능을 추가하려면, 서브클래스를 생성하는 것보다 융통성 있는 방법을 제시한다.

*랩퍼(Wrapper)* 라고도 불린다.

## Utility

---

- 동적으로 또한 투명하게(transparent), 즉 다른 객체에 영향을 주지 않고 개개의 객체에 새로운 책임을 추가 할 때
- 제거될 수 있는 책임에 대해 사용
- 실제 상속으로 서브클래스를 계속 만드는 방법이 실질적이지 못할 때
  - 너무 많은 수의 독립된 확장이 가능할 때 모든 조합을 지원하기 위해 이를 상속으로 해결하고자 하면 클래스의 수가 너무 많아지게 된다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/decor064.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/decor066.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/decor065.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/decor067.gif)

### Elements

- **Component**
  - *VisualComponent*에 해당
  - 동적으로 추가할 서비스를 가질 가능성이 있는 객체에 대한 인터페이스
- **ConcreteComponent**
  - *TextView*에 해당
  - 추가저긴 서비스가 실제로 정의되어야 할 필요가 있는 객체
- **Decorator**
  - *Component* 객체에 대한 참조자를 관리하면서 *Component*에 정의된 인터페이스를 만족하도록 인터페이스를 정의
- **ConcreteDecorator**
  - *BorderDecorator*, *ScrollDecorator*에 해당
  - *Component*에 새롭게 추가할 서비스를 실제로 구현하는 클래스

*Decorator*는 자신의 *Component* 객체 쪽으로 요청을 전달한다. 요청 전달 전 및 전달 후에 자신만의 추가 연산을 선택적으로 수핼할 수도 있다.

## Feature

---

### 단순한 상속보다 설계의 융통성을 더 많이 증대시키는 것이 가능

*Decorator Pattern*은 객체에 새로운 행동을 추가할 수 있는 가장 효과적인 방법이다.

*Decorator*를 사용하면 *Decorator*를 객체에 연결하거나 분리하는 작업을 통해 새로운 책임을 추가하거나 삭제하는 일이 런타임에 가능해 진다. 그러나 상속은 코드에서, 즉 정적으로 새로운 클래스를 추가해야만 추가적인 행동을 정의할 수 있는 방법이다.

*Decorator Pattern*을 사용하면, *Decorator*의 조합을 통해 새로운 책임도 조합할 수 있다.
이는 구조에 나타나는 바와 같이 *Decorator* 클래스와 *Component* 클래스 사이에 집합 관계가 정의되어 있어 *Decorator*는 *Component* 클래스의 인스턴스를 포함할 수 있는데, 이는 *Component*의 서브 클래스인 *ConcreteDecorator_A* 클래스나 *ConcreteDecorator_B* 클래스의 인스턴스를 포함할 수 있다는 의미가 된다.
이렇게 *Decorator*의 서브클래스를 아무런 코드의 수정없이 포함할 수 있으므로, *Decorator* 클래스의 서브클래스를 여러개를 포함하면 여러 책임을 조합하는 결과가 된다.

### 클래스 계통의 상부측 클래스에 많은 기능이 누적되는 상황을 회피

*Decorator Pattern*은 책임 추가 작업에서 "필요한 비용만 그때 지불하는" 방법을 제공한다. 즉, 지금까지 예상하지 못한 특성들을 한번에 다 개발하기 위해 고민하고 노력하기보다는 발견하지 못하고 누락된 서비스들을 *Decorator* 객체를 통해 지속적을 추가할 수 있다. 기능은 단순한 구성요소들의 조합으로 얻어질 수도 있다.

### *Decorator*와 해당 그 *Decorator*의 구성요소가 동일한 것은 아님

*Decorator*는 사용자에게 일관된 인터페이스를 제공하기 위한 껍데기이다. 그러므로 객체 식별자 관점에서 구성요소롸 이를 둘러싼 *Decorator* 객체가 동일한 식별자를 가질 필요는 없다.

### *Decorator*를 사용함 으로써 작은 규모의 객체가 다수 생성

클래스들이 어떻게 조합하여 새로운 모습과 기능을 만들어 내는가에 따라서 새로운 객체가 계속 만들어지게 된다. 이러한 객체들은 상호작용하는 방법이 서로다르지만, 클래스가 다르거나 변수에 정의된 값이 다른것이 아니다.

## Implementation

---

### Considerations point in implementaion

#### 인터페이스 일치시키기

*Decorator* 객체의 인터페이스는 반드시 자신을 둘러싼 구성요소의 인터페이스를 만족해야 한다. 따라서 *ConcreteDecorator* 클래스는 동일한 부모 클래스를 상속해야 한다.

#### 추상 클래스로 정의되는 *Decorator* 클래스 생략하기

간혹 추상 클래스인 *Decorator* 클래스를 정의할 필요가 없을 때가 있다. 이러한 경우에, *Decorator* 클래스에 정의할 책임은, 구성요소에게 요청을 전달해야하는 한가지 책임만 존재하게 된다. 이 책임은, 새로운 클래스들을 설계할 때 발생한다기 보단, 기존에 존재하는 클래스 계통을 사용해야 할 떄 자주 일어난다. 이때 구성요소에게 요청을 전달하는 *Decorator* 클래스의 책임을 *ConcreteDecorator* 클래스와 합칠수 있다.

#### *Component* 클래스는 가벼운 무게를 유지시키기

인터페이스를 만족하는지 확인하려면 구성요소와 *Decorator* 모두 동일한 부모 클래스인 *Component* 클래스를 상속받아야 한다. 이때 *Component* 클래스를 가볍게 정의하는것은 중요한 일이다.

가볍게 장의한다는 의미는 연산에 해당하는 인터페이스만을 정의하고 무언가 저장할수 있는 변수는 정의하지 말라는 의미이다. 데이터를 저장하는 일은 서브클래스에서 할 일이다.

#### 객체의 겉을 변경할 것인가, 속을 변경할 것인가

흔히 *Decorator Pattern*은 행동을 변경할 수 있도록 객체에 외장을 입힌것으로 알고 있다. 즉 객체의 겉을 변경시켜 새로운 서비스, 행동을 추가하여 행할 수 있도록 한다.

*Decorator Pattern*은 객체의 외관을 변경하는 것이므로, 구성요소는 자신을 둘러싼 *Decorator*에 대해서는 전혀 알 필요가 없다. 즉 *Decorator*는 구성요소에 대해 투명하다고 할 수 이다.

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/deco-069.gif)

*Strategy Pattern*은 *Decorator Pattern*과 반대로 객체의 내부를 변경하는 대표적인 패턴이다.
*Component* 클래스가 본질적으로 매우 복잡하고 무거운 특성을 가진다면, *Strategy Pattern*이 더 나은 해결방안이다.  *Strategy Pattern*에서 구성요소는 자신이 받은 처리 요청을 분리된 *Strategy* 클래스에 전달한다. *Strategy* 객체의 대체를 통해서 구성요소의 기능성을 변경하거나 확작하는 방법을 제공한다.

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/deco-068.gif)

### Implement Example

```cpp
/**
* @brief Component - Viewr
* @details 추후 서비스가 추가될 인터페이스 정의
*/
class Viewer {
public:
  virtual ~Viewer() {}

  virtual void view() const = 0;
};

/**
* @brief ConcreteComponent - BasicViewr
* @details 인터페이스의 기본 행동 구현
*/
class BasicViewr : public Viewer{
public:
  
  ...

  void view() const override{ m_window->show();};

private:
  Window* m_window = nullptr;
};

/**
* @brief Decorator - ViewrDecorator
* @details 기존의 인터페이스에 동작을 추가하기 위해 중간 Decorator 클래스 정의
*/
class ViewrDecorator : public Viewer{
public:
  ViewrDecorator(Viewer* viewer) : m_viewer(viewer) {}
  virtual ~ViewrDecorator() {}

  virtual void view() const override{ m_viwer->view(); };

private:
  Viewer* m_viwer = nullptr;
};

/**
* @brief ConcreteDecorator - CameraViewer
* @details 새롭게 추가할 행동 정의
*/
class CameraViewer : public ViewrDecorator {
public:
  CameraViewer(Viewer* viwer) : ViewrDecorator(viwer) {
    m_cam = new Camera();
    m_cam->open();
  }

  ~CameraViewer() {
    m_cam->close();
    delete m_cam;
  }

  void view() const override {
    // 기존에 정의된 인터페이스에 새로운 행동 추가
    getCameraImage();
    ViewrDecorator::view();
  }

private:
  void getCameraImage() { m_cam->getImage2Buffer(); }
private:
  Camera* m_cam;
};

...

  // 기본이 되는 동작을 구현한 ConcreteComponent 객체 생성
  Viewer* viewer = dynamic_cast<Viewer*>(new BasicViewr());

  // 기본동작을 가지는 ConcreteComponent에 새로운 서비스를 추가하기위해,
  // Decorator를 상속한 ConcreteDecorator를 객체를 생성
  viewer = dynamic_cast<Viewer*>(new CameraViewer(viewer));
  viewer = dynamic_cast<Viewer*>(new LidarViewer(viewer));
  viewer = dynamic_cast<Viewer*>(new GNSSViewer(viewer));
  
  // 새로운 서비스가 추가된 인터페이스 실행
  viewer->view();

...

```

## Related Pattern

---

*Decorator Pattern*은 *Adapter Pattern*과 관련되어 있다. *Adapter*는 인터페이스를 변경시켜주는 것이지만, *Decorator*는 객체의 책임, 행동을 변화 시킨다.

*Composite Pattern*과도 관련된다. *Decorator*는 한 구성요소만을 가지는 *Composite*라고 볼수 있다. 그러나 이 목적은 객체의 합성이 아니라 객체에 새로운 행동을 추가하기 위한 것이다.

*Strategy Pattern*과도 관련된다. *Decorator*는 객체의 겉모양을 변경하고, *Strategy*는 객체의 내부를 변화시킨다. 객체를 변경하는 두가지 대안인 셈이다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
