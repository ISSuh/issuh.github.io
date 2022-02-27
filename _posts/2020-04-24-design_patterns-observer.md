---
title: Design Patterns - Observer
date: 2020-04-24 16:44:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Observer
- C++
---

# Behavioral Patterns - Observer

## Intent

---

객체 사이에 일 대 다의 의존 관계를 정의해 두어, 어떤 객체의 상태 변할 때 그 샛체에 의존성을 가진 다른 객체들이 그 변화를 통지 받고 자동으로 갱신될 수 있도록 한다.

종속자(Dependent), 게시-구독(Publish-Subscribe) 라고도 불린다.

## Utility

---

- 어떤 추상 개념이 두 가지 양상을 가지고 하나가 다른 하나에 종속적일 때
  - 각 걍상을 별도로 객체로 캡슐하여 이들 각각을 재사용 할 수 있다.
- 한 객체에 가해진 변경으로 다른 객체를 변경해야 하고, 프로그래머들은 얼마나 많은 객체들이 변경되어야 하는지 몰라도 될 때
- 어떤 객체가 다른 객체에 다른 변화를 통보할 수 있는데, 그 변화에 관심있어 하는 객체들이 누구인지에 대한 가정 없이도 그러한 통보가 될 때

## Structure

---

### Basic Structure

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/observer.gif)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/obser023.gif)

### Elements

- **Subjects**
  - *Observer*들을 알고 있는 주체
  - 임의 개수의 *Observer* 객체는 *Subject*를 감시
  - *Subject*는 *Observer* 객체를 붙이거나 떼는데 필요한 인터페이스를 제공
- **ConcreteSubjects**
  - *ConcreteObserver* 객체에게 알려주어야 하는 상태를 저장
  - 상태가 변경될 떄 *Observer*에게 통보
- **Observers**
  - *Subject*에 생긴 변화에 관심 있는 객체를 갱신하는데 필요한 인텀페이스를 정의
  - *Subject*의 변경에 따라 변화되어야 하는 객체들의 일관성 유지
- **ConcreteObservers**
  - *ConcreteSubject* 객체에 대한 참조자를 관리
  - *Subject*의 상태와 일관성을 유지해야 하는 상태를 저장
  - *Subject*의 상태과 *Observer*의 상태를 일관되게 유지하는 데 사용되는 사용자 인터페이스를 구현

*ConcreteSubject*는 *Observer*의 상태와 자신의 상태가 달라지는 변경이 발생할 떄마다 *Observer*에게 통보한다.

*ConcreteSubject*에서 변경이 통보된 후, *ConcreteObserver*는 필요한 정보를 *Subject*에게 질의하여 얻어온다. *ConcreteObserver*는 이 정보를 이용하여 *Subject*의 상태와 자신의 상태를 일치시킨다.

다음 다이어그램은 하나의 *Subject*와 두 *Observer* 사시의 협력관계를 표현한 것이다.

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/obser022.gif)

*Notify()* 연산은 *Subject*로만 호출되는 것이 아니고, *Observer*나 다른 객체들로도 호출이 가능하다.

## Feature

---

#### *Subject*와 *Observer* 클래스간에는 추상적인 결합도만이 존재한다

*Subject*가 아는 것은 *Observer*들의 리스트일 뿐이다.

*Observer*들은 *Observer* 클래스에 정의된 인터페이스를 따르지만, *Subject*는 어떤 *ConcreteObserver* 클래스가 있는지에 대해서는 알 필요가 없다. 그러므로 *Subject*와 *Observer* 간의 결합도는 추상적이며, 그 조차도 최소화 되어있다.

*Subject*와 *Observer* 클래스가 강결합되어 있지 않기 때문에, 이들은 시스템의 여러 추상화 계층에 속할 수 있다. 하위 수준의 *Subject*라 할지라도 상위 수준의 *Observer*와 연결되어 교류할 수 있다.

#### *Broadcast* 방식의 교류를 가능하게 한다

*Observer Pattern*에서 *Subject*가 보내는 통보는 구체적인 수신자를 지정할 필요가 없다. 이 통보는 *Subject*의 정보를 원하는 모든 객체에 자동으로 전달되어야 한다.

*Subject*는 얼마나 많은 객체들이 이 변화 정보를 원하는지 알 필요가 없으며, 단지 자신의 *Observer*에게만 상태 변화 사실을 알려주면, *Observer*가 이 통보를 처리할지 혹은 무시할지를 결정한다.

#### 예측하지 못한 정보를 갱신한다

*Observer*는 다른 *Observer*의 존재를 모르기 때문에 *Subject*를 변경하는 비용이 궁극적으로 어느 정도인지 모른다.

*Subject*에 계속적으로 어떤 연산이 가해질 떄, *Observer*와 *Subject*에 종속된 다른 객체들의 연속적인 수정을 일으킬수 있게 된다. 게다가, 잘 정의된 것도, 잘 유지된 것도 아닌 종속성 기준 때문에 불필요한 갱신이 일어날 수 있게된다.

이러한 문제는, 단순한 갱신 프로토콜로는 *Subject*의 무엇을 변경했는지에 대한 상세한 정보를 알 수 없다는 사실 때문에 더욱 좋지 않다. *Observer*가 무엇이 변했는지 알 수 있게 해 주는 별도의 프로토콜이 추가되지 않는한, 변경을 유추하는 작업은 어렵게 된다.

## Implementation

---

### Considerations point in implementaion

#### *Subject*와 그에 해당하는 *Observer*를 대응

자신이 통보해 주어야 하는 *Observer*들을 *Subject*가 지속적으로 관리하도록 해야 한다.

이를 구현하기 위해 *Subject*에 *Observer*에 대한 참조자를 저장하는 방법과, 별도의 탐색용 자료구조(해시 테이블 등)를 두고 *Subject*와 *Observer* 간의 대응 관계를 관리하게 하는 방법이 있다.

#### 하나 이상의 *Subject*를 감시

어떨 때는 하나 이상의 *Subject*에 종속된 *Observer*가 있을 수 있다.

*Observer*가 어떤 *Subject*에서 통보가 전달되었는지 알아야 한다면 *Update()* 연산을 확장할 필요가 있다. *Subject*는 *Update()* 연산에서 자신을 매개변수화하여 *Observer*가 어떤 *Subject*를 확인하고 있는지 알 수 있도록 한다.

#### 갱신을 촉발(trigger)시키는 존재

*Subject*와 *Observer*가 각각 자신의 값을 정확하게 유지하기 위해서는 통보 매커니즘에 의존할 수 밖에 없다.

값을 갱신하기 위해, 어느 객체에서 *Notify()* 연산을 호출할 것인지에 대해 두가지 선택사항이 있다.

- *Subject* 클래스의 상태 변경 후 상태를 지정하는 연산에서 *NOtify()* 연산을 호출
  - *Subject*가 정의한 *Notify()* 를 사용자가 호출할 필요없다.
  - 계속되는 연산의 수행으로 여러 번 수정해야 하므로 비효율적이다.
- 사용자가 적시에 *Notify()* 연산을 호출하는 책임을 짐
  - 사용자가 일련의 상태 변경이 될 떄까지 갱신의 시작을 미룰 수 있기 때문에, 중간 중간 불필요한 수정이 일어나지 않는다.
  - 사용자측에서 *Notify()* 연산의 호출을 책임을 지므로, 해당 연산을 호출하는 것을 잊는 등의 오류가 있을 수 있다.

#### 삭제한 *Subject*에 대한 무효(Dangling) 참조자를 계속 유지할 때

*Subject*의 삭제로 *Observer*가 무효 참조자를 가자게 되도록 만들면 안된다.

무효 참조자를 피하는 한가지 방법은 *Subject*가 *Observer*에게 "자신이 삭제되었으니 *Observer*에 대한 참조자를 지워라" 라고 통보하는 것이다. 다른 객체들이 *Observer*를 참조할 수 있고, 또 *Observer*들이 다른 *Subject*를 관찰해야 하기 때문에, 이러한 *Observer*의 삭제는 있을 수 있는 일이다.

#### 통보 전에 *Subject*의 상태가 자체 일관성을 가지도록 보장

*Observer*는 자신의 상태를 변경하기 위해 *Subject*에게서 현재 상태를 질의한다. 이러한 자체 일관성(self- consistency) 규칙은 *Subject* 클래스에 정의한 연산이 상속한 연산을 호출하게 되면 뜻하지 않게 깨지기 쉽다.

```cpp
/**
* 기반클래스의 연산을 먼저 호출하고 자신의 상태를 바꾸게 될 떄,
* 기반클래스의 연산으로 통보가 되고 자신이 변경한 상태는 반영되지 않는다.
*/
void MySubject::Operation(int newValue){
  // 통보를 실행
  BaseClassSubject::Operation(newValue);

  // 이미 늦은 서브클래스의 상태를 변경
  m_myValue += newValue;
}
```

이 문제를 피하기 위해, *Subject* 클래스에 *Template Method Pattern*을 적용하여 이 *Template Method*를 통해 통보를 처리한다.

```cpp
/**
* 재정의할 서브클래스에서 오버라이드할 기본 연산을 정의하고,
* Template Method의 마지막 연산에서 실제 Notify() 연산을 수행한다.
* 이렇게 하면, *Subject* 연산을 서브클래스에서 오버라이드할 때 그 객체는
* 자기 일관성을 유지할 수 있게 된다.
*/
void Text::Car(TextRange r){
  // 이 부분은 후에 서브클래스가 재정의
  ReplaceRange(r);

  notify();
}
```

여담으로, 어떤 *Subject* 클래스가 통지를 촉발시키는지에 대한 사항을 문서화하면 도움이 많이 된다.

#### *Observer*별 갱신 프로토콜의 회피(Push Model & Pull Model)

*Observer Pattern*을 구현하려고 변경이 발생할 떄, *Subject*가 추가적인 정보를 브로드캐스트하게 만들 때가 많다. *Subject*는 *Update()* 연산의 인자로 이 추가적인 정보를 전달해야 한다.

*Subject*가 자신의 변경에 대한 상세한 정보를 *Observer*에게 전달하는 *Push Model*을 이용할 수 있다. 이 모델에서는 *Subject*가 *Observer*의 요청이 무엇인지 알아야 한다. 즉, *Subject* 클래스는 *Observer* 클래스에 대한 어떤 가정을 하게 되는데, 이런 가정이 항상 맞는것은 아니기 때문에 *Observer* 클래스의 재사용성이 떨어지게 된다

*Subject*가 최소한의 정보만을 전달하고 *Observer*가 다시 상세 정보를 요청해 오는 *Pull Model*을 이용할 수 있다. 이 모델에서는 *Subject*가 *Observer*를 몰라도 된다. *Pull Model*을 사용하면 *Observer* 클래스가 *subject*와 상관없이 무엇이 변했는지를 확인해야 한다는 점에서 비효율적일 수 있다.

#### 자신이 관심 있는 변경이 무엇인지 명확하게 지정

*Subject* 클래스에 자신이 관심 있는 이벤트에 대한 *Observer*를 등록하는 인터페이스를 정의함으로써 갱신의 과정을 좀더 효율화할 수 있다. 이렇게 관심 있는 이벤트가 발생할 때, *Subject*는 등록된 *Observer*에게만 알려주면 된다.

특정 이벤트를 관심있는것으로 등록하려면, *Subject*는 다음 연산을 이용하여 *Observer*를 등록한다.

```cpp
void Subject::Attach(Observer*, Aspect& interest);
```

여기서 *interest* 매개변수는 관심있는 이벤트를 의미한다. 실제로 통보가 일어날때, *Subject*는 *Update()* 연산의 매개변수로 관심 있는 변경된 내용을 전달한다.

```cpp
void Observer::Update(Subject*, Aspect& interest);
```

#### 복잡한 갱신의 의미 구조를 캡슐화

*Subject*와 *Observer* 간에 일어나는 관련성이 복잡하다면, 이들 관련성을 관리하는 별도의 객체를 만들도록 한다.

이 객체의 목적은 *Observer*가 처리해야 하는 *Subject*의 변경처리를 최소화 하는것이다. 예를 들어, 연산의 수행으로 여러개의 서로 관련된 *Subject*를 변경해야 한다고 가정했을 떄, 모든 *Subject*가 수정되고 나서, 이 *Subject*의 *Observer*에게는 딱 한 번만 통보 되어야 한다. 즉 *Observer*들에게 두 번 이상 통보되는 것을 피해야 한다.

이러한 *Subject*와 *Observer* 사이의 관련성을 관리하는 별도의 객체는 다음과 같은 세가지를 책임져야 한다.

- *Subject*와 *Observer*를 매핑하고 이를 유지하는 인터페이스를 정의해야 한다.
  - *Subject*는 자신들의 *Observer*가 누구인지, *Observer*는 자신들의 *Subject*가 누구인지 관리할 필요가 없어진다.
- 관련성 관리 객체는 특별한 갱신 전략이 필요하다.
- 관련성 관리 객체는 *Subject*에게 요청이 있을 때, 모든 독립적 *Observer*들을 다 수정해야 한다.

다음의 다이어그램은 이 관련성 관리 객체(*ChageManager*)와 *Subject*, *Observer* 간의 관계도 이다.

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/obser025.gif)

하나의 *ChageManager* 클래스가 여러 객체 간의 상태 변화를 총괄한다는 점에서 *ChageManager* 클래스는 일종의 *Mediator Pattern*의 예 이다. 일반적으로 이 *ChageManager* 클래스만 존재하므로, 이 클래스 객체는 전역적으로 *Singleton Pattern*을 통해 정의하는 것이 유용하다.

### Implement Example

```cpp
/**
* @brief Subject - Subject
* @details
*/
class Subject {
public:
  virtual ~Subject();
  
  virtual void attach(std::string& id, Observer* obj) {
    m_objMap[id] = obj;
  }

  virtual void detatch(std::string& id) {
    m_objMap.erase(id);
  };

  virtual void notify(){
    for(const auto& obj : m_objMap){
      obj.update(this);
    }
  }

protected:
  Subject();

private:
  std::map<std::string, Observer*> m_objMap;
};

/**
* @brief ConcreteSubject - DataBase
* @details
*/
class ClockTimer : public PushServer {
public:
  Timer();

  virtual int getHour();
  virtual int getMinute();
  virtual int getSecond();
  
  void tick() {
    // 시간 상태 변경

    ...

    // 등로된 Observer들에게 통지
    notify();
  }

private:
  
  ...

}

/**
* @brief Observer - Observer
* @details
*/
class Observer {
public:
  virtual ~Observer();
  virtual void update(Subject* chaingedSubject) = 0;

protected:
  Observer();
};

/**
* @brief ConcreteObserver - ClientA
* @details
*/
class DigitalClock : public Widget,
                     public Observer {
public:
  DigitalClock(const std::string& id, ClockTimer* timer) : m_id(id),
                                                           m_subject(timer){
    m_subject->attach(m_id,this);
  }

  virtual ~DigitalClock() {
    m_suject->detatch(m_id);
  }

  virtual void update(Subject* chaingedSubject){
    // 통지해온 Subject와 내가 바라보고 있는 Subject가 일치하는지 확인
    if(chaingedSubject == m_subject){
      draw();
    }
  }
  
  virtual void draw() {
    // Subject로 부터 새로운 값을 얻어옴
    int hour = m_subject->getHour();
    int minute = m_subject->getMinute();
    int second = m_subject->getSecond();

    ...

  }

private:
  const std::string m_id;
  ClockTimer* m_subject;
}


...

  // Subject 객체 생성
  ClockTimer* timer = new ClickTimer();

  // Observer 객체 생성 및 Subject 등록
  DigitalTimer* digitalTimer = new DigitalTimer("Digital", timer);
  AnalogTimer* analogTimer = new AnalogTimer("Analog", timer);

  // Subject의 상태 변경
  // 이후 등록된 모든 Observer에게 상태 변경 통지
  timer->tick();

...

```

## Related Pattern

---

복잡한 갱신의 의미 구조를 캡슐화함으로써, *ChangeManager* 객체는 *Subject*와 *Observer* 사이의 "Mediator" 역할을 한다. 또한 *ChangeManager* 객체는 *Singleton Patten*을 사용하여 시스템에 하나만 존재하고 전역적으로 접급이 가능하도록 할 수 있다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
