---
title: Design Patterns - Strategy
date: 2020-04-25 14:32:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Strategy
- C++
---

# Behavioral Patterns - Strategy

## Intent

---

동일 계열의 알고리즘군을 정의하고, 각 알고리즘을 캡슐화하며, 이들을 상호교환이 가능하도록 만든다.

알고리즘을 사용하는 클라이언트와 상관없이 독립적으로 알고리즘을 다양하게 변경할 수 있게 한다.

정책(Policy)이라고도 불린다.

## Utility

---

- 행동들이 조금씩 다를 뿐 개념적으로 관련된 많은 클래스들이 존재할 때
  - *Strategy Pattern*은 많은 행동 중 하나를 가진 클래스를 구성할 수 있는 방법을 제공한다.
- 알고리즘의 변형이 필요할 때
  - 예를 들어, 기억공간과 처리속도 간의 절충에 따라 서로 다른 알고리즘을 정의할 수 있을 것이다. 이러한 변형물들이 알고리즘의 상속 관계로 구현될 떄 *Strategy Pattern*을 사용할 수 있을것이다.
- 사용자가 몰라야 하는 데이터를 사용하는 알고리즘이 있을 떄
  - 노출하지 말아야 할 복잡한 자료구조는 *Strategy* 클래스에만 두면 되므로 사용자는 몰라도 된다.
- 하나의 클래스가 많은 행동을 정의하고, 어떤 행동들이 그 클래스의 연산 안에서 복잡한 다중 조건문의 모습을 취할 때
  - 많은 조건문보다는 각각을 *Strategy* 클래스로 옮겨놓는 것이 좋다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/strategy.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/strat011.gif)

### Elements

- **Strategy**
  - *Composite*에 해당
  - 제공하는 모든 알고리즘에 대한 공통의 연산들은 인터페이스로 정의
  - *Comtext* 클래스는 *ConcreteStrategy* 클래스에 정의한 힌터페이스를 통해서 실제 알고리즘을 사용
- **ConcreteStrategy**
  - *SimpleCompositer*, *TeXcompositor*, *ArrayCompositor*에 해당
  - 인터페이스를 통해 실제 알고리즘을 구현
- **Context**
  - *Composition*에 해당
  - *ConcreteStrategy* 객체를 통해 구성
  - *Strategy* 객체에 대한 참조자를 관리하고, 실제로는 *Strategy* 서브클래스의 인스턴스를 가지고 있음으로써 구체화 함
  - *Strategy* 객체가 자료에 접근해가는 데 필요한 인터페이스를 정의

#### Elements Communication

*Strategy* 클래스와 *Context* 클래스는 의사교환을 통해 선택한 알고리즘을 구현한다. 즉, *Context* 클래스는 알고리즘에 해당하는 연산이 호출되면, 알고지름 처리에 필요한 모든 데이터를 *Strategy* 클래스로 보낸다. 이때, *Context* 객체 자체를 *Strategy* 연산에 인자로 전송할 수도 있다.

*Context* 클래스는 사용자 쪽에서 온 요청을 각 *Strategy* 객체로 전달한다. 이를 위해 사용자는 필요한 알고리즘에 해당하는 *ConcreteStrategy* 객체를 생성하여 이를 *Context* 클래스에 전송하는데, 이 과정을 거치면 사용자는 *Context* 객체와 전달한 *ConcreteStrategy* 객체와 함께 동작한다.

## Feature

---

#### 동일 계열의 관련 알고리즘군이 생긴다

*Strategy* 클래스 계층은 동일 계열의 알고리즘군 혹은 행동군을 정의한다. 이러한 알고리즘 자체의 재사용도 가능하다. 즉, 상속을 통해 알고리즘 공통의 기능성들을 추출하고 이를 재사용 할 수 있다.

#### 서브클래싱을 사용하지 않는 대안이다

상속은 다양한 알고리즘이나 행동을 지원하는 또 다른 방법이다.

서로 다른 행동을 제공하기 위해 *Context* 클래스를 직접 상속받을 수도 있다. 그러나 이렇게 행동의 처리 방법을 *Context*에 직접 하드코딩하게 되면, 후에 수정이 어렵게 된다. 그러므로 다양한 알고리즘을 만들어 낼 수 없게된다.

알고리즘을 *Strategy* 클래스로 독립시키면 *Context*와 무관하게 알고리즘을 변형시킬수 있고, 알고리즘을 바꾸거나 이래하거나 확장하기도 쉬워진다.

#### 조건문을 없앨 수 있다

*Strategy Pattern*을 사용하면 원하는 행동들을 선택하는 조건문을 없앨 수 있다.

```cpp
// Strategy Pattern 미사용시 조건문을 통해 알고리즘을 선택한다
void Composittion::repair() {
  switch(m_breakingStrategy) {
  case SimpleStrategy :
    ComposeWithSimpleCompositor()
    break;
  case Texstrategy :
    ComposeWithTexCompositor()
    break;

    ...

  }

  // 필요하다면 기존 복합으로 결과를 병합한다.

  ...

}
```

```cpp
// Strategy Pattern 사용시 조건문이 필요없게 된다.
void Composittion::repair() {
  m_compositor->compose();

  // 필요하다면 기존 복합으로 결과를 병합한다.

  ...

}
```

#### 구현의 선택이 가능하다

동일한 행동에 대하여 서로 다른 구현을 제공한다. 사용자는 서로 다른 시간과 공간이 필요한 여러 *Strategy*들 중 하나를 선택할 수 있다.

#### 사용자(프로그램)은 서로 다른 전략을 알아야 한다

동일한 행동에 대하여 서로 다른 전략을 선택하기 전에 전략들이 어떻게 다른지 이해해야 한다. 즉, 사용자는 구현 내용을 모두 알고 있어야 하며, 사용자가 이렇게 서로 다른 행동 각각의 특징을 미리 알고 있을 때 *Strategy Pattern*을 사용할 수 있다.

#### *Strategy* 객체와 *Context* 객체 사이에 의사소통 오버헤드가 있다

서브클래스에서 구현할 알고리즘의 복잡함과는 상관없이 모든 *ConcreteStrategy* 클래스는 *Strategy* 인터페이스를 공유한다. 따라서 어떤 *ConcreteStrategy* 클래스는 이 인터페이스를 통해 들어온 모든 정보를 다 사용하지 않는데도 이 정보를 떠안아야 할 경우가 생기게된다. 즉, 사용하지도 않을 매개변수를 *Context* 객체가 생성하고 초기화할 때도 있다는 말이 된다.

위와같은 상황이라면, *Strategy*와 *Context* 클래스 사이에 좀더 높은 결합도가 필요하게 된다.

#### 객체의 수가 증가한다

*Strategy*들로 인해 응용프로그램 내의 객체 수가 증가한다.

간혹, *Context* 객체들이 공유 가능한 상태 없는 객체로서 *Strategy*를 구현하여 불필요한 오버헤드를 줄일 수도 있다. 이때. 처리에 필요한 상태는 *Context* 클레스에서 관리한다. 공유한 *Strategy*들은 호출사이의 상태를 관리하지 말아야 한다.

이러한 방법을 더울 자세히 만든 것이 바로 *Flyweight Pattern* 이다.

## Implementation

---

### Considerations point in implementaion

#### *Strategy* 및 *Context* 인터페이스를 정의

*Strategy*와 *Context* 인터페이스는 *ConcreteStrategy*가 *Context*에서 어떠한 정보가 필요할 떄, 효율적으로 접근 가능해야 하고, 그 반대로 *Context*에서 *ConcreteStrategy*의 접근 또한 가능하도록 만들어져야 한다. 이러한 접근을 구현하기 위해 아래 두가지 방법이 있다.

- *Context*가 *Strategy*의 연산 쪼으로 데이터를 매개변수에 담아 보내지도록 한다.
  - 데이터를 *Strategy*으로 보내는 것이다.
  - *Strategy* 클래스와 *Context* 클래스 간의 결합도를 낮출수 있다.
  - *Context*는 *Strategy*에 필요없는 데이터를 전달할 수도 있다.
- *Context* 객체가 자기 자신을 *Strategy* 객체에 인자로 보내고, *Strategy* 객체는 *Context* 객체에서 데이터를 요청 하거나, *Strategy* 클래스는 *Context* 클래스에 대한 참조정보를 저장한다.
  - *Strategy* 객체가 *Context*에 정의된 인터페이스를 이용하여 정의한 정보들을 매번 얻어야 하기 때문에, *Context*는 데이터에 접근할 수 있는 인터페이스를 더욱이 정교하게 정의해야 한다.
  - *Strategy* 클래스와 *Context* 클래스간 결합도가 높아진다.

#### 전략을 템플릿 매개변수로 사용

C++의 템플릿 기능을 사용하여 *Strategy*을 가진 클래스를 구성할 수 있다. 이 기법은 두가지 조건이 만족되어야만 적용가능하다.

- *Strategy* 객체를 컴파일 타임에 결정할 수 있을 떄
- *Strategy* 객체가 런타임에 바꿀 필요가 없을 때

두 조건이 만족하면, 구성할 클래스를 템플릿 클래스로 정의하고, *Strategy* 클래스 이 템플릿의 매개변수로 정의한다.

```cpp
template<class Astrategy>
class Context {
  void operate() { m_strategy.doAlgorithm(); }

  ...

private:
  Astrategy m_strategy;
};

class MyStrategy {
public:
  void doAlgorithm();
};


...

  Context<MyStrategy> context;

...

```

템플릿을 이용하면 *Strategy* 인터페이스를 정의하는 추상 클래스를 정의할 필요가 없게된다. *Strategy*를 템플릿의 매개변수로 사용하기 때문에 *Strategy*와 *Context*는 정적으로 바인딩됨으로써 효율이 증가하는 효과를 볼수 있게 된다.

#### *Strategy* 객체에 선택성을 부여

*Strategy* 객체를 가지지 않는 편이 나을 때, *Context* 객체를 단순하게 만들 수도 있다.

*Context*는 *Strategy* 객체가 접근하기 전 자신에게 *Strategy* 객체가 있는지를 확인한다. *Strategy* 객체가 있다면 그냥 그것을 사용하고, 없다면 기본 행동을 수행한다. 이 방법은 사용자가 기본 행동을 사용해도 무방하다면 *Strategy* 객체를 꼭 사용하지 않아도 된다는 이점이 있다.

### Implement Example

```cpp
/**
* @brief Context - Container
* @details 구현한 ConcreteStrategy를 선택하여 해당 객체가 가지는 알고리즘 동작
*/
class Container {
public:
  Container(Filter* filter) : m_filter(Container) {}

  // 선택한 ConcreteStrategy의 알고리즘을 실행하는 인터페이스 정의 및 구현
  void separateData() {
    m_filter->filtering(m_vec);
  }

  ...

private:
  Filter* m_filter = nullptr;
  std::vector<int> m_vec;
};

/**
* @brief Strategy - Filter
* @details 구현할 알고리즘의 기본이 되는 인터페이스 정의
*/
class Filter {
public:
  // ConcreteStrategy에서 구현해야할 알고리즘의 인터페이스 정의
  virtual void filtering() = 0;

protected:
  Filter();
};

/**
* @brief ConcreteStrategy - OddFilter
* @details Strategy의 인터페이스를 구현하여 독자적인 알고리즘을 가짐
*/
class OddFilter : public Filter {
public:
  OddFilter();

  // 자신만의 알고리즘 구현
  void filtering(std::vector<int>& vec) override {
    std::vector<int> oddVec;

    for(const auto* i : vec){
      if(i & 1 != 0)
        oddVec.push_back();
    }

    vec.clear();
    vec.resize(oddVec.size());
    std::copy(oddVec.begin(), oddVec.end(), vec.begin());
  }
};

/**
* @brief ConcreteStrategy - EvenFilter
* @details Strategy의 인터페이스를 구현하여 독자적인 알고리즘을 가짐
*/
class EvenFilter : public Filter {
public:
  EvenFilter();

  // 자신만의 알고리즘 구현
  void filtering() override {
    std::vector<int> evenVec;

    for(const auto* i : vec){
      if(i & 1 == 0)
        evenVec.push_back();
    }

    vec.clear();
    vec.resize(evenVec.size());
    std::copy(evenVec.begin(), evenVec.end(), vec.begin());
  }
};


...

  // Context 객체 및 Strategy 선택
  Container* oddContainer = new Container(new OddFilter());  
  Container* evneContainer = new Container(new EvenFilter());  

  ...

  // 자신이 선택한 ConcreteStrategy의 알고리즘 실행
  oddContainer->separateData();
  evneContainer->separateData();

...

```

## Related Pattern

---

*Strategy* 객체는 규모가 작은 클래스들이므로 *Flyweight Pattern*으로 정의하는것이 바람직하다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
