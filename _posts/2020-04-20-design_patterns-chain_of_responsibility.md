---
title: Design Patterns - Chain of Responsibility
date: 2020-04-20 17:19:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Chain of Responsibility
- C++
---

# Behavioral Patterns - Chain of Responsibility

## Intent

---

메세지를 보내는 객체와 이를 받아 처리하는 객체들 간의 결합도를 없애기 위한 패턴이다.

하나의 요청에 대한 처리가 반드시 한 객체에서만 되지 않고, 여러 객체에게 그 처리 기회를 주려는 것이다.

## Utility

---

- 하나 이상의 객체가 요청을 처리해야 하고, 그 요청 처리자 중 어떤 것이 *선행자(Priori)* 인지 모를 때.
  - 처리자가 자동으로 확정되어야 한다.
- 메세지를 받을 객체를 명시하지 않은 채 여러 객체 중 하나에게 처리를 요청하고 싶은 때
- 요청을 처리할 수 있는 객체 집합이 동적으로 정의되어야 할 때

## Structure

---

### Basic Structure

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/chain094.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/chain089.gif)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/chain090.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/chain091.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/chain092.gif)

### Elements

- **Handler**
  - *HelpHandler*에 해당
  - 요청을 처리하는 인터페이스를 정의
  - 후속 처리자(*successor*)와 연결을 구현
  - 연결 고리에 연결된 다음 객체에게 다시 메세지를 보냄
- **ConcreteHandler**
  - *PrintButton*, *PrintDialog*에 해당
  - 책임져야 할 행동이 있다면 스스로 요청을 처리하여 후속 처리자에 접근
  - 자신이 처리할 행동이 있으면 처리하고, 그렇지 않으면 후속 처리자에 다시 처리를 요청
- **Client**
  - 객체에게 필요한 요청을 보냄

사용자는 처리를 요청하고, 이 처리 요청은 실제로 그 요청을 받을 책임이 있는 *ConcreteHandler* 객체를 만날 때까지 정의된 연결 고리를 따라서 계속 전달된다.

## Feature

---

### 객체 간의 행동적 결합도가 적어짐

다른 객체가 요청을 어떻게 처리하는지 몰라도 된다. 단지 요청을 보내는 객체는 이 메세지가 적절하게 처리될 것이라는 바램만 있으면 된다. 메세지를 보내는 쪽이나 받는 쪽 모두 서로를 모르고, 또 연결된 객체들 조차도 그 연결 구조가 어떻게 되는지 모른다. 자신은 단순하게 자신과 연결된 단 하나의 후보 객체만 알면 된다.

결과적으로 이 패턴은 객체들 간의 상호작용 과정을 단순화 시킨다.

### 객체에게 책임을 할당하는 것에 대한 유연성 증가

객체의 책임을 여러 객체에게 분산 시킬수 있으므로 런타임에 객체 연결 고리를 변경하거나 추가하여 책임을 변경하거나 확장 할 수 있다.

### 메세지 수신이 보장되지 않음

어떤 객체가 이 처리에 대한 수신을 담당한다는 것을 명시하지 않았으므로 요청이 처리된다는 보장이 없다. 만약 객체들 간의 연결 고리가 잘 정의되지 않는다면, 요청을 처리되지 못한 채로 버려질 수 있다.

## Implementation

---

### Considerations point in implementaion

#### 후속 처리자들의 연결 고리 구현

후속 처리자들의 연결 고리를 구현하는 방법에는 크게 두가지가 있다.

- 새로운 연결을 만드는 것
  - 일반적으로는 *Handler* 클래스에 정의하지만, *ConcreteHandler* 클래스에 정의할 수도 있다.
- 이미 있는 연결 정보를 사용하는 것

기존 연결을 사용하는 방법은 그 연결이 지금 필요한 연결 정보를 제공할 떄 적절한 방법이다. 명시적으로 별도의 연결 정보를 정의할 필요가 없기 때문이다. 그러나 필요한 연결 고리를 정의할 수 없을 떄라면 중복되더라도 별도의 연결 정보를 위한 변수를 정의해야 한다.

#### 후속 처리자 연결

연결 정보를 정의하기 위해 미리 정의된 참조자가 없다면 직접 정의해야 한다. 이때 *Handler* 클래스는 요청 처리에 대한 인터페이스를 정의할 뿐만 아니라, 후속 처리자에 대한 정보 저장의 인스턴스 변수도 정의해야한다. 이 정보를 이용하여 *Handler* 클래스의 기본 구현은 자신의 후속 처리자로 정의된 객체에 *HandleRequest()* 메세지를 전달하도록 만든다.

*ConcreteHandler* 서브클래스가 요청받은 메세지에 대해 처리할 내용이 없으면, 기반 클래스에 정의된 구현을 재정의 할 필요 없이 자신의 후속 처리자로 정의된 객체에 메세지를 그대로 전달하면 된다.

후속 처리자에 대한 연결을 유지하는 *HelpHandler*의 기본 클래스의 예는 아래와 같다.

```cpp
class HelpHandler {
public:
  HelpHandler(HelpHandler* s) : _successor(s) {}
  
  virtual void HelpHandle() {
    if(_successor) {
      _successor->HandleHelp();
    }
  }

private:
  HelpHandler* _successor;
}
```

#### 처리 요청의 표현부를 정의

처리 요청을 정의하는 방법에는 여러가지가 있다.

첫번째 방안은, 처리를 코드화 하여 개매변수로 받아들이도록 하는 처리자 함수를 하나만 정의하는 것이다. 이렇게 하면 얼마든지 처리 요철을 정의할 수 있다. 이와같이 구현하기 위해선, 메세지 송신과 수신 측 사이에 처리 요청을 어떨게 코드로 만들지에 대해 서로 규약을 정의해야 한다.

두번째 방안은, 코드에 따라서 대응하는 처리 요청을 발생시키기 위해 조건문을 정의한다. 만약 매개변수를 전송할 안정적인 방법이 없다면 수동으로 매개변수를 만들고 해석해야 한다. 그러나 이 방법은 정의한 연산을 직접 호출하는 방법보다 안전하지 못하다.

세번째 방안은, 매개변수 전송문제를 처리하기 위해, 매개변수를 묶어 별도의 객체로 만드는 것이다. 예를들어, *Request* 클래스를 이용해서 처리 요청을 명시적으로 정의한다. 만약 새로운 처리 요청이 발생하면, *Request* 서브클래스로 정의하면 된다. 상속을 이용해서 서로 다른 매개변수를 정의하는 것이다. *Handle*은 어떤 종류의 처리 요청인지만 알면 필요한 매개변수에 접근할 수 있다.

처리요청을 식별하기 위해서 *Request* 클래스는 클래스에 대한 식별자를 반환하는 멤버 함수를 정이해야 한다. 그렇게 되면, 메세지 수신 측에서 런타임의 타입 정보를 이용할 수 있다. 그러나 이 방법은 언어가 런타임에 타입 정보를 제공하는 기능이 있어야 한다.

다음 예는 처리 요청을 식별하기 위해 *Request* 객체를 사용하는 디스패처(dispatch) 함수의 바탕이다.

```cpp
void Handler::HandleRequest(Request* theRequest) {
  switch(theRequest->GetKind()) {
  case Help:
    // 인지를 적당한 타입으로 변경
    HandleHelp((HelpRequest* theRequest));
    break;
  case Print:
    // 인지를 적당한 타입으로 변경
    HandlePrint((PrintRequest*) theRequest);
    // ...
    break;
  default:
    // ...
    break;
  }
}
```

위 예시의 *HandleRequest()* 연산을 상속한 후 재정의 하면 새로운 처리 방법을 구현할 수 있다. 서브클래스는 관심있는 처리 요청만 담당하도록 정의하면 된다. 다른 처리 요청은 기반 클래스에서 정의된 대로 처리하면 된다.

```cpp
class ExtendedHandler : public Handler {
public:
  virtual void HandleRequest(Request* theRequest){
    switch(theRequest->GetKind()) {
    case Preview:
      // 요청 처리
      break;
    default:
      // 기반 클래스인 Handler가 다른 요청 처리
      Handle::HandleRequest(theRequest);
      break;
    }
  }
  
  ...

};
```

### Implement Example

```cpp

enum class EVENT : int {
  BUTTON_PRESS,
  BUTTON_RELEASE,
  WINDOW_SHOW,
  WINDOW_HIDE,
  ON_REGIST,
  ...

}

/**
* @brief Handler - EventHandler
* @details 요청을 처리하기 위한 인터페이스 정의
*/
class EventHandler {
public:
  EventHandler(EventHandler* sub = nullptr) : m_evnetHandler(sub) {}
  virtual ~EventHandler() {}

  virtual void handler(EVENT eventCode) {
    if(m_evnetHandler != nullptr)
      m_evnetHandler->handler(eventCode);
  }

private:
  EventHandler* m_evnetHandler = nullptr;
};

/**
* @brief ConcreteHandler - Widget
* @details 하위 연결고리들의 인터페이스 정의
*/
class Widget : public EventHandler {
public:
  Widget(Widget* parent = nullptr) : EventHandler(parent), m_parent(parent) {}
  virtual ~Widget() {}

private:
  Widget* m_parent;
}

/**
* @brief ConcreteHandler - Window
* @details 요청 받은 내용을 처리하기 위해 연결된 하위 요청 처리자
*/
class Window : public Widget {
public:
  Window(EventHandler* sub = nullptr) : Widget(sub) {}

  void handler(EVENT eventCode) override {
    switch(eventCode){
    case EVENT::WINDOW_SHOW:

      ...

      break;

    case EVENT::WINDOW_HIDE:

      ...

      break;

    default:
      // 해당 클레스에서 처리하지 못하는 요청일때,
      // 요청을 기반클래스로 전파
      EventHandler::handler(eventCode);
      break;
    }
  }

  ...

};


/**
* @brief ConcreteHandler - Button
* @details 요청 받은 내용을 처리하기 위해 연결된 하위 요청 처리자
*/
class Button : public Widget {
public:
  Button(EventHandler* sub  = nullptr) : Widget(sub) {}

  void handler(EVENT eventCode) override {
    switch(eventCode){
    case EVENT::BUTTON_PRESS:

      ...

      break;

    case EVENT::BUTTON_RELEASE:

      ...

      break;

    default:
      // 해당 클레스에서 처리하지 못하는 요청일때,
      // 요청을 기반클래스로 전파
      EventHandler::handler(eventCode);
      break;
    }
  }

  ...

};

/**
* @brief ConcreteHandler - Application
* @details 요청 받은 내용을 처리하기 위해 연결된 하위 요청 처리자
*/
class Application : public EventHandler {
public:
  Application(EventHandler* sub = nullptr) : EventHandler(sub) {}

  void handler(EVENT eventCode) override {
    switch(eventCode){
    case EVENT::ON_REGIST:

      ...

      break;

    default:
      // 해당 클레스에서 처리하지 못하는 요청일때,
      // 요청을 기반클래스로 전파
      EventHandler::handler(eventCode);
      break;
    }
  }

  ...

}


...

  // 처리에 대한 연결 고리 설정
  // app --> window --> btn 순으로 전파
  Button* btn = new Button();
  Window* window = new Winddow(btn);
  Application* app = new application(window);

  // 발생한 이벤트에 대해 처리 요청
  app->handler(EVENT::WINDOW_HIDE);

...

```

## Related Pattern

---

*Chain of Responsibility Pattern* 은 *Composite Pattern*과 함께 대부분 사용되는데, 이때 구성요소의 부모는 후속 처리자 처럼 동작한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
