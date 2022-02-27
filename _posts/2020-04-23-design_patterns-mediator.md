---
title: Design Patterns - Mediator
date: 2020-04-23 22:08:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Mediator
- C++
---

# Behavioral Patterns - Mediator

## Intent

---

한 집합에 속해있는 객체의 상호작용을 캡슐화하는 객체를 정의한다.

객체들이 서로를 참조하지 않도록 하여 객체 사이의 소결합(Loose Coupling)을 촉진시키며, 개발자가 객체의 상호작용을 독립적으로 다양화 시킬수 있게 만든다.

## Utility

---

- 여러 객체가 잘 정의된 형태이기는 하지만 복잡한 상호작용을 기질 때
- 객체간의 의존성이 구조화되지 않으며, 잘 이해하기 어려울 떄
- 한 객체가 다른 객체를 너무 많이 참조하고, 너무 많은 의사소통을 수행해서 그 객체를 재사용하기 힘들 때
- 여러 클래스에 분산된 행동들이 상속 없이 상황에 맞게 수정되어야 할 떄

## Structure

---

### Basic Structure

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/mediator.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/media030.gif)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/fontc047.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/media033.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/media031.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/media034.gif)

### Elements

- **Mediator**
  - *DialogDirector*에 해당
  - *Colleague* 객체와 교류하는 데 필요한 인터페이스를 정의
- **ConcreteMediator**
  - *FontDialogDirector*에 해당
  - *Colleague* 객체와 조화를 이워서 협력 행동을 구현
  - 자신이 맡을 *Colleague*를 파악하고 관리
- **Conlleague 클래스들**
  - *ListBox*, *EntryField*에 해당
  - 다른 객체와 통신이 필요하면 그 *Mediator*를 통해 통신되도록 하는 동료 객체를 나타내는 클래스
  - 자신의 *Mediator* 객체가 무엇인지 파악

*Colleague* 는 *Mediator*에서 요청을 송수신한다. *Mediator*는 필요한 *Colleague*사이에 요청을 전달할 의무가 있다.

## Feature

---

#### 서브클레싱을 제한한다

*Mediator*는 다른 객체 사이에 분산된 객체의 행동들을 하나의 객체로 국한한다. 이 행동을 변경하고자 할때, *Mediator* 클래스를 상속하는 서브클래스만 만들면 된다. *Colleague* 클래스는 여전히 재사용 가능하다.

#### *Colleauge* 객체 사이의 종속성을 줄인다

*Mediator*는 행동에 참여하는 객체간의 소결랍을 증진시킨다. 이로써 *Mediator* 클래스와 *Colleague* 클래스 각각을 독립적으로 다양화 시킬수 있고 재사용할 수 있다.

#### 객체 프로토콜을 단순화 한다

*Mediator*는 다 대 다의 관계를 일 대 다의 관계로 축소시킨다.

#### 객체 간의 협력 방법을 추상화 한다

객체 사이의 중재를 독립적인 개념으로 만들고 이것을 캡슐화함으로써, 사용자는 각 객체의 행동과 상관없이 객체간 연결방법에만 집중할 수 있다. 결과적으로 시스템에서 객체가 어떻게 동작하는지를 좀더 명확하게 할 수 있다.

#### 통제가 집중화 된다

*Mediator Pattern*은 상호작용의 복잡한 모든 것들이 자신 내부에서만 오가게 한다.

*Mediator* 객체는 동료 객체 간의 상호작용에 관련된 프로토콜을 모두 캡슐화 하기 때문에 어느 동료 객체 보다도 훨씬 복잡해질 수 있다. 이 때문에 *Mediator* 클래스 자체의 유지보수가 어려워 질 수 있다.

## Implementation

---

### Considerations point in implementaion

#### 추상 클래스인 *Mediator* 생략

만약 관련 객체들이 오직 하나의 *Mediator* 클래스와 동작한다면 *Mediator*를 추상 클래스로 정의할 필요가없다.

추상 클래스의 목적은 앞으로 또 다른 상호작용을 정의할 새로운 *Mediator* 서브클래스를 만들 때를 대비하는 것이다.

#### *Colleague* 객체 - *Mediator* 객체 간의 의사소통

*Mediator* 클래스를 구현하는 한가지 방법은 *Observer Pattern*을 사용하는 것이다.

*Colleague* 객체가 주관자(subject) 객체로 동작하여 상태의 변화가 일어날 때마다 *Mediator*에게 이를 통보하면, *Mediator*는 처리방법에 따라 다른 객체에게 변경을 통보하여 처리한다.

다른 방법으로는 *Mediator* 클래스 내 특화된 통지(Notification) 인터페이스를 정의하여 *Colleague* 객체들이 직접 통신하게 만드는 것이다.

### Implement Example

```cpp
/**
* @brief Mediator - EventHandle
* @details Colleague 객체들 사이의 통신을 위한 인터페이스 정의
*/
class EventHandle {
public:
  virtual ~EventHandle() = default;
  virtual void eventHaneler(Widget* widget, EventCode eventCode) = 0;

  void addWidget(Widget* widget) {
    m_widgetMap[widget->getWidgetID()] = widget;
  }

protected:
  EventHandle();

private:
  std::map<std::string, Widget*> m_widgetMap;
};

/**
* @brief ConcreteMediator - UIEventHandle
* @details Colleague 객체들 사이의 통신을 위한 인터페이스 구현
*/
class UIEventHandle {
public:

  // 상태 변화 발생시, 해당 변화에 대해 영향이 있는 다른 Colleague 객체에게 전달
  void eventHaneler(Widget* widget, EventCode eventCode)) override {
    switch(widget->getWidgetType()){

    case WIDGET_TYPE_BUTTON: {

      if(widget->getWidgetID() == "MyButton"){
        m_widgetMap["MyPictureView"]->show();

        ...

      }
      ...

      break;
    }

    case WIDGET_TYPE_TEXTAREA: {

      ...

      break;
    }

    default:
      break;
    }
  }
};

/**
* @brief Colleague - Widget
* @details Colleague 객체들의 인터페이스 정의
*/
class Widget {
public:
  Widget(cosnt std::string& id, EventHandle* handler) :
    m_widgetID(id), m_eventHandle(handler) {}

  virtual ~Widget();

  void setWidgetType(WidgetType type)  { m_type = type; }
  WidgetType getWidgetType() const  { return m_type; }

  std::string getWidgetID() const  { return m_widgetID; }

  ...

private:
  std::string m_widgetID = "";
  WidgetType m_type;
  EventHandle* m_eventHandle = nullptr;

  ...

};


/**
* @brief Colleague - Button
* @details Colleague 객체들의 인터페이스 구현
*/
class Button : public Widget {
public:
  Button(cosnt std::string& id, EventHandle* handler) : Widget(id, handler) {
    setWidgetType(WIDGET_TYPE_BUTTON);
    m_eventHandle->addWidget(this);

    ...

  }

// 행동들 정의
private :
  void onButtonClick() {
    m_eventHandle->eventHaneler(this, EVENT_BUTTON_CLICK);
  }

  void onButtonPress() {
    m_eventHandle->eventHaneler(this, EVENT_BUTTON_PRESS);
  }

  ...

};

/**
* @brief Colleague - TextArea
* @details Colleague 객체들의 인터페이스 구현
*/
class TextArea : public Widget {
  Button(cosnt std::string& id, EventHandle* handler) : Widget(id, handler) {
    setWidgetType(WIDGET_TYPE_TEXTAREA);
    m_eventHandle->addWidget(this);

    ...

  }

// 행동들 정의
private:
  void onWrite() {
    m_eventHandle->eventHaneler(this, EVENT_TEXTAREA_WIRTE);
  }

  void onDelete() {
    m_eventHandle->eventHaneler(this, EVENT_TEXTAREA_DELETE);
  }

  ...

};


...

  // Mediator 객체 생성
  UIEventHandle* handle = new UIEventHandle();

  // Colleague 객체들 생성 및 Mediator 객체 등록
  Button* btn = new Button("MyButton", handle);
  TextArea* textArea = new Button("MyTextArea", handle);

...

```

## Related Pattern

---

*Facade Pattern*은 객체들로 구성된 서브시스템을 추상화하여 좀더 편한 인터페이스를 제공하려는 것으로 *Mediator Pattern*과는 다르다. *Facade* 객체는 서브시스템을 구성하는 객체로만 메세지가 전달되고, 반대로 서브시스템을 구성하는 객체가 *Facade* 객체에 메세지전달은 처리되지 않는다. 그러나 *Mediator* 객체는 양방향이다.

싱호 관련된 객체들은 *Observer Pattern*을 이용해서 *Mediator* 객체들과 교류한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
