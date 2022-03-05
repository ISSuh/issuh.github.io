---
title: Design Patterns - Facade
date: 2020-04-18 13:22:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns
- Facade
- C++
---

# Structural Patterns - Facade

## Intent

---

한 서브시스템 내의 인터페이스 집합에 대한 획일화된 하나의 인터페이스를 제공하는 패턴으로, 서브시스템을 사용하기 쉽도록 상위 수준의 인터페이스를 정의한다.

## Utility

---

- 복잡한 서브시스템에 대한 단순한 인터페이스 제공일 필요할 때
  - *Facade Pattern*은 서브시스템에 대한 단순하면서도 기본적인 인터페이스를 제공함으로써 대부분의 개발자들에게 적합한 클래스 형태를 제공한다.
- 추상 개념에 대한 구현 클래스와 사용자 사이에 너무 많은 종속성이 존재할 때
  - *Facade*의 사용을 통해 사용자와 다른 서브시스템 간의 결합도를 줄일 수 있다.
  - 서브시스템에 정의된 모든 인터페이스가 공개되면 빈번한 메서드 호출이 있을수 있으나, 이런 호출은 단순화 형태로 통합하여 제공하고 나머지 부분은 내부적으로 처리함으로써 사용자와 서브시스템 사이의 호출횟수는 실질적으로 감소하게 되는 효과를 가진다.
- 서브시스템을 계층화시킬 때
  - *Facade Pattern*을 사용하여 각 서브시스템의 계층에 대한 접근점을 제공한다.
  - 서브시스템이 다른 서브시스템에 종속적이라 하더라고, 각자가 제공하는 *Facade*를 통해서만 대화를 진행하게 함으로써 서브시스템 간의 종속성을 줄일 수 있다.
  - 서브시스템 내부 설계의 변경이 다른 서브시스템에 독립적으로 자유롭게 될 수 잇다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/facade.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/facad057.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/facad059.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/facad058.gif)

### Elements

- **Facade**
  - *Compiler*에 해당
  - 단순하고 일관된 통합 인터페이스를 제공
  - 서브시스템을 구성하는 어떤 클래스가 어떤 요청을 처리해야 하는지에 대한 관리
  - 사용자의 요청을 해당 서브시스템 객체에 전달
- **SubSystem Classes**
  - *Scanner*, *Parser*, *ProgramNode*등 에 해당
  - 서브시스템의 기능을 구현하고, *Facade* 객체로 할당된 작업을 실제로 처리하지만 *Facade*에 대한 아무런 정보를 가지고 있지 않다. 즉, 이들에 대한 어떠한 참조자도 가지고 있지 않다.

## Feature

---

### 서브시스템의 구성요소 보호

사용자가 다루어야 할 객체의 수가 줄어들며, 서브시스템을 쉽게 사용할 수 있도록 한다.

### 서브시스템과 사용자 코드 간의 결합도 약화

서브시스템내 정의된 요소들은 강하게 결합될 수 있다. 그러나 *Facade*로 인하여 사용자 코드와 서브시스템은 분리되어 있기 때문에 결합도를 약화시킨다.

서브시스템과 사용자 간의 결합이 약하게 되면, 서브시스템 내의 요소를 다양화하는 작업을 원활하게 할 수 있다.

### 응요읖로그램 쪽에서 서브시스템 클래스를 사용하는 것을 완전히 막지 않는다

사용자는 *Facade*를 사용하여 서브시스템에 접근할 것인지, 서브시스템 클래스를 직접 사용할 것인지 결정할 수 있다.

## Implementation

---

### Considerations point in implementaion

#### 사용자와 서브시스템 간의 결합도 줄이기

사용자와 서브시스템 간의 의존성을 줄이는 방안으로는 크게 두가지가 있다.

*Facade*를 추상 클래스로 정의하고 서브시스템을 나타내기 위해 이를 상속하는 구체 서브클래스를 정의하여 다른 구현을 정의하도록 하는 것이다. 그러면, 사용자는 *Facade*만 사용하기 때문에, 어떤 서브시스템의 구현이 사용되고 있는지 알 필요가 없다.

서브클래싱을 하지 않고, 다른 서브시스템의 객체를 조합하여 *Facade*를 구성할 수도 있다. 또한 *Facade*를 수정하기 위해서는 하나 이상의 서브시스템 객체를 대체하면 된다.

#### 서브시스템 클래스 중 공개할 것과 감출 것

서브시스템은 클래스와 유사하다. 둘 다 인터페이스를 가지고 있으며 무엇인가를 감춘다. 클래스는 상태와 연산을, 서브시스템은 클래스를 캡슐화 한다. 클래스의 인터페이스를 정의할 때 공개할지 말지를 고민하는 것처럼 서브시스템의 인터페이스도 공개할지 말지를 결정해야 한다.

서브시스템의 공개 인터페이스는 사용자가 직접 접근할 수 있는 클래스들이고, 비공개 클래스는 서브시스템 자체가 된다. *Facade* 클래스는 공개 인터페이스의 일부이다.

### Implement Example

```cpp

/**
* @brief SubSystem - CameraViewer
* @details 하위 시스템 구현
*/
class Window {
public:
  Window(const int width, const int height)
    : m_width(width), m_width(height) {}

  void setScreenBuffer(Buffer* buf) { m_buf = buf; }

  void showOneFrame() {

    ...

    m_buf->pop(m_img);

    ...

  }

  ...

private:

  ...
  
  const int m_width;
  const int m_height;
  Buffer* m_buf = nullptr;

  ...

};

/**
* @brief SubSystem - Camera
* @details 하위 시스템 구현
*/
class Camera {
public:
  Camera(Buffer* buf) : m_buf(buf) {}

  ...

  void getImage() const {

    ...

    m_buf->push(img);
  }

private:

  ...

  Buffer* m_buf = nullptr;
  Image img;
};

/**
* @brief SubSystem - Buffer
* @details 하위 시스템 구현
*/
class Buffer {
public:
  Buffer(const int size) : m_buf(std::queue<uint8_t*>(size, 0) {}

  void push(const Image& img) {
    if (m_maxSize > 0 && m_queue.size() > m_maxSize)
      return;

    m_queue.push(data);
  }

  void pop(Image& img) {
    if (m_buf.empty())
      return;

    img = m_queue.front();
    m_queue.pop();
  }

private:
  std::queue<Image> m_buf;
};

/**
* @brief Facade - CameraViewer
* @details SybSystem을 기반으로 통합 인터페이스를 구성
*/
class CameraViwer {
public:
  void initialize() {
    m_buf = new Buffer(MAX_BUF_SIZE);

    m_window = new Window(WIDTH, HEIGHT);
    m_window->setScreenBuffer(m_buf);

    m_cam = new Camera(m_buf);
  }

  void viewing() {
    while(g_run){
        ...

        m_cam()->getImage();

        m_window->showOneFrame()

        ...
    }
  }

  ...

private:
  ...

  Window* m_window = nullptr;
  Buffer* m_buf = nullptr;
  Camera* m_cam = nullptr;

  ...
};

...

  CameraViwer camView;

  camView.initialize();
  camView.viewing();

...

```

## Related Pattern

---

*Abstract Factory Pattern*은 서브시스템에 독립적인 방법으로, 서브시스템 객체를 생성하는 인터페이스를 제공하기 위해 *Facade*와 함께 사용할 수 있다. *Abstract Factory*는 *Facade*에 대한 대안으로서, 플렛폼에 종속적인 클래스를 감추는데 사용한다.

*Mediator Pattern*도 기존에 존재하는 클래스의 기능성을 추상화한다는 점에서 *Facade Pattern*과 유사하다.
*Mediator*의 목적은 여러 객체들 사이의 협력 관계를 추상화하여 기능성의 집중화를 막는것 이다. *Mediator Pattern*에 참여하는 객체는 서로를 직접 알지 못하고 단지 중재자를 통해서만 상호작용이 된다. 이에 비해 *Facade*는 서브시스템 인터페이스 자체를 추상화하여 사용을 용이하게 사려는 목적을 가지고 있다. 즉, 새로운 기능성을 추가할 수도 없고, 이런 새로운 추가 기능에 대해서는 알 수도 없다.

*Facade*객체가 하나만 있어도 된다면, *Singleton*을 이용하여 구현한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
