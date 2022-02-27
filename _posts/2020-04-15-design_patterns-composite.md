---
title: Design Patterns - Composite
date: 2020-04-15 13:05:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns
- Composite
- C++
---

# Structural Patterns - Composite

## Intent

---

부분과 전체의 계층을 표현하기 위해 객체들을 모아 트리 구조로 구성한다.

사용자로 하여금 개별 객체와 복합 객체를 모두 동일하게 두룰수 있게 한다.

## Utility

---

- 부분-전체의 객체 계통을 표현하고 싶을 때
- 사용자가 객체의 합성으로 생긴 복합 객체와 개개의 객체 사이의 차이를 알지 않고도 자기 일을 할 수 있도록 만들고 싶을 때
  - 사용자는 복합 구조(Composite Structue)의 모든 객체를 똑같이 취급하게 된다.

## Structure

---

### Basic Structure

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/compo072.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/compo073.gif)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/compo075.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/compo074.gif)

### Elements

- **Component**
  - *Graphic*에 해당
  - 집합관계에 정의될 모든 객체에 대한 인터페이스를 정의
  - 모든 클래스에 해당하는 인터페이스에 대해서는 공통의 행동을 구현
  - 전체 클래스에 속한 요소들을 관리하는 데 필요한 인터페이스를 정의
  - 순환 구조에서 요소들을 포함하는 전체 클래스로 접근하는 데 필요한 인터페이스를 정의 및 구현
- **Leaf**
  - *Rectangle*, *Line*, *Text*, ... 에 해당
  - 자식이 없는 객체를 나타냄
  - 객체 합성에 가장 기본이 되는 객체의 행동을 정의
- **Composite**
  - *Picture*에 해당
  - 자식이 있는 구성요소에 대한 행동을 정의
  - 자신이 복합하는 요소들을 저장하면서, *Composite* 인터페이스에 정의된 자식 관련 연산을 구현
- **Client**
  - 인터페이스를 통해 복합 구조 내의 객체들을 조작

사용자는 복합 구조 내 객체 간의 상호작용을 위해 *Component* 클래스 인터페이스를 사용한다.

요청받은 대상이 *Leaf* 인스턴스이면 자신이 정의한 행동을 직접 수행하고, 대상이 *Composite*이면 자식 객체들에게 요청을 위힘한다. 위임하기 전후에 다른 처리를 수행할 수도 있다.

## Feature

---

### 기본 객체와 복합 객체로 구성된 하나의 일관된 클래스 계통을 정의

기본 객체는 더 복합적인 객체들에 속해 있을수 있다. 그러나 사용자 코드는 일반화된 상위 개념의 객체를 조작하는 방식으로 프로그래밍을 하면, 런타임 기본 객체와 복합 객체를 구분하지 않고 일관되게 프로그래밍이 가능해 진다.

### 사용자의 코드 단순화

사용자는 객체의 특성이 복합 구조인지 단일 구조인지 모르고도 개발을 진행 할 수 있다.

### 새로운 구성요소의 쉬운 추가

새롭게 정의된 *Composite*나 *Leaf*의 서브클래스들은 기존에 존재하는 구조들과 독립적으로 동작이 가능하게 된다. 즉, 새로운 요소가 추가되었다고 해서 사용자의 프로그램이 변경될 필요가 없게 된다.

### 설계가 지나치게 범용성을 많이 가짐

새로운 요소를 쉽게 추가할 떄의 단점은 *composite*의 구성요소에 대한 제약을 가하기 힘들어 진다는 것이다.

가끔 *composite*가 오직 한 개의 구성요소만 가졌으면 할 때가 있지만, *Composite* 클래스만으로 타입 시스템을 통해 이러한 제약을 가할수가 없다. 런타임 점검이 들어가야 한다.

## Implementation

---

### Considerations point in implementaion

#### 포함 객체에 대한 명확한 참조자

자식 구성요소에서 부모를 가리키는 참조자를 관리하면 *composite* 구조의 관리를 단순화할 수 있다. 부모에 대한 참조자는 구조를 거슬러 올라가거나 요소를 하나 삭제하는 과정을 단순화 시킨다.

*composite* 구조가 중첩될 떄, *composite* 구조의 모든 자식들이 또 다른 부모가 되는데, 부모의 참조자가 있으면 이런 *composite* 구조에서는 반복적으로 자신들의 부모에 대한 참조가 가능해진다.

#### *Component* 인터페이스를 최소화

*Composite Pattern*의 주요 목표중 하나는 사용자가 어떤 *Leaf*나 *Composite* 클래스가 존재하는지 모르도록 하는 것이다.

위와같은 목표를 달성하기위해, *Component* 클래스는 *Leaf*와 *Composite*에 정의된 모든 공통의 연산을 다 정의하고 있어야 한다. *Component* 클래스는는 이들 연산에 대한 기본 구현을 제공하고 *Leaf*와 *Composite* 클래스가 이를 재정의 한다.

그러나 이런 목표가 종종 상속 구조의 중요한 원칙와 충돌할 때가 있다. 즉, *Component* 클래스에서는 서브클래스인 *Leaf*가 정의하지 않는 연산도 정의해야 한다. 또한 *Lead* 클래스에는 의미 없고 *Composite* 클래스만 의미 있는 연산도 *Component* 클래스에 정의해야한다.

이러한 충돌을 회피하기 위해, 자식들에 접근하는 인터페이스를 예를 들면, *Component* 클래스의 자식을 처리하는 연산의 기본 구현 사항으로 아무 것도 반환하지 않도록 기본 구현을 만든다. 그리고 *Leaf* 클래스는 이 구현을 그대로 사용하고, *Composite* 클래스는 자식을 반환하도록 재정의 한다.

#### 자식을 관리하는 연산 선언

*Composite* 클래스가 *Add()*와 *Remove()* 연산을 통해 자식들을 관리하기는 하지만, *Composite Pattern*에서 매우 중요한 관심사는 *Composite* 클래스 계통 내의 어느 클래스에 이 연산을 선언할 것인가 결정하는 것이다.

이 결정은 크게 투명성과 안전성 사이를 선택해야하는 결정이다.

##### 투명성

**자식을 관리하는 인터페이스를 클래스 계통의 최상위 계층에 정의하면**, 서브클래스 모두에게 동일한 인터페이스가 유지되어 이를 사용하는 사용자에게 인터페이스의 투명성을 부여할 수 있다.

반대로, 사용자가 *Leaf* 클래스의 인스턴스에게 *Add()*나 *Remove()* 연산을 호출하는 의미없는 행동을 하지 않도록 안전성 유지를 위한 비용을 지불해야 한다.

##### 안전성

***Composite* 클래스에만 자식을 관리하는 연산을 정의한다면**, 이를 사용하는 사용자는 아예 *Leaf* 클래스의 인스턴스에 이런 연산을 요청하지 않을 것이므로 안전성을 보장받는다.

그러나 *Leaf* 클래스와 *Composite* 클래스가 서로 다른 인터페이스를 가지게 되므로 사용자는 이를 동일한 대상으로 간주하고 사용할 수 없게 되어 투명성을 잃어버리게 된다.

#### *Component*가 *Component*의 리스트 구현 가능여부

자식들의 집합을 *Component* 클래스의 인스턴스 변수로 관리하고자 할 수도 있다. 그러나 최상위 클래스에  자식 포인터를 정의하는 것은, 자식들을 전혀 가지고 있지 않은 모든 *Leaf* 클래스의 인스턴스들도 이 집합을 관리하기 위한 메모리를 정의해야 한다는 것이므로 바람직하지 않은 방법이다.

이러한 방법은 자식의 갯수가 적을때만 효과적인 방법이다.

#### 자식사이의 순서

자식간의 순서가 의미 있고 문제가 될 떄는, 자식에게 접근, 관리하는 인터페이스를 설계시 자식들의 순서를 관리할 수 있도록 주의를 기울여야 한다.

#### 성능 개선의 위한 캐실(Caching)

*composite* 구조 내부를 수시로 순회하고 탐색해야 한다면, *Composite* 클래스는 자식을 순회하는 정보를 미리 담고 있을 수도 있다.

그러나 구성요소가 변경되면 부모가 캐싱하는 정보는 의미가 없어진다. 그러므로 구성요소가 자신의 부모가 누구인지 아는 상황에서만 이 구현이 의미가 있다.

캐싱을 이용하려면, 현재 저장된 캐시의 내용이 유효한지 아닌지 확인하는 연산이 필요하다.

#### 누가 구성요소를 삭제하는 책임을 가질지에 대한 여부

가비지 컬렉션의 기능을 제공하지 않는 언어에서는 자식이 없어질 떄 *Composite* 클래스가 보통 그 삭제의 책임을 가진다. 그러나 *Leaf* 객체가 변경될 수 없는 객체이거나 공유될 수 있는 객체라면 예외적으로 삭제할 수 없다.

#### 구성요소를 저장하기 위해 가장 적당한 데이터 구조

*Composite*는 매우 다양한 데이터 구조를 이용하여 자식들을 저장할 수 있는데, Linked List, Arrary, Tree, Hash Table 모두가 사용될 수 있다. 어떤 데이터 구조를 사용할 것인가는 상황에 맞춰 효율적인 데이터 구조를 선택하도록 한다.

### Implement Example

```cpp
/**
 * @brief Component - Window
 * @details 집합관계에서 포함되는 인터페이스를 정의
 */
class Window {
 public:
  virtual ~Window() = default;

  // 구성요소들의 공통 인터페이스 정의
  virtual const std::string& getName() const { return m_name; }
  virtual void printElementList() = 0;

  // 자식요소 관리를 위한 인터페이스 정의
  virtual void add(Window* window) {};
  virtual void remove() {};

 protected:
  Window(const std::string& name) : m_name(name) {};

 private:
    const std::string m_name;
};

/**
* @brief Composite - MediaWindow
* @details 하위 구성요소를 가지지면서 관련 인터페이스를 구현 및 정의
*/
class MediaWindow : public Window {
public:
  MediaWindow(const std::string& name) : Window(name) {};
  virtual ~MediaWindow() {
    for(auto& e : m_elements){
      delete e;
    }
  }

  // 구성요소들의 공통 인터페이스 구현
  void printElementList() override {
    std::cout << this->getName() << std::endl;

    for(const auto& e : m_elements){
      e->printElementList();
    }
  }

  // 자식요소 관리를 위한 인터페이스 구현
  void add(Window* window) override { m_elements.push_back(window); }
  void remove() override { m_elements.pop_back(); }

private:
  std::vector<Window*> m_elements;
};

/**
* @brief Composite - ImageWindow
* @details 하위 구성요소를 가지지면서 관련 인터페이스를 구현 및 정의
*/
class ImageWindow : public Window {
public:
  ImageWindow(const std::string& name) : Window(name) {};
  virtual ~ImageWindow() {
    for(auto& e : m_elements){
      delete e;
    }
  }

  // 구성요소들의 공통 인터페이스 구현
  void printElementList() override {
    std::cout << this->getName() << std::endl;

    for(const auto& e : m_elements){
      e->printElementList();
    }
  }

  // 자식요소 관리를 위한 인터페이스 구현
  void add(Window* window) override { m_elements.push_back(window); }
  void remove() override { m_elements.pop_back(); }

private:
  std::vector<Window*> m_elements;
};

/**
* @brief Leaf - TitleBar
* @details 자식이 없으며, Component의 인터페이스를 자신에 맞게 구현
*/
class TitleBar : public Window{
public:
  TitleBar(const std::string& name) : Window(name) {}
  virtual ~TitleBar() {}

  // 구성요소들의 공통 인터페이스 구현
  void printElementList() override { std::cout <<  getName() << std::endl; }
};

/**
* @brief Leaf - MenuBar
* @details 자식이 없으며, Component의 인터페이스를 자신에 맞게 구현
*/
class MenuBar : public Window{
public:
  MenuBar(const std::string& name) : Window(name) {}
  virtual ~MenuBar() {}

  // 구성요소들의 공통 인터페이스 구현
  void printElementList() override { std::cout <<  getName() << std::endl; }
};

/**
* @brief Leaf - VideoPlayer
* @details 자식이 없으며, Component의 인터페이스를 자신에 맞게 구현
*/
class VideoPlayer : public MediaWindow{
public:
  VideoPlayer(const std::string& name) : MediaWindow(name) {}
  virtual ~VideoPlayer() {}

  // 구성요소들의 공통 인터페이스 구현
  void printElementList() override { std::cout <<  getName() << std::endl; }
};

...

  // Composite 객체 생성
  MediaWindow* mainWindow = new MediaWindow("MyMediaWindow");
  VideoPlayer* videoPlayer = new VideoPlayer("MyVideoWindow");
  ImageWindow* imageWindow = new ImageWindow("MyImageWindow");

  // Leaf 객체 생성
  TitleBar* mainTitleBar = new TitleBar("MyMediaWindowTitleBar");
  TitleBar* videoTitleBar = new TitleBar("MyVideoWindowTitleBar");
  TitleBar* imageTitleBar = new TitleBar("MyImageWindowTitleBar");

  // Leaf객체를 Composite의 자식으로 구성
  mainWindow->add(mainTitleBar);
  mainWindow->add(new MenuBar("MyMediaWindowMenuBar"));

  videoPlayer->add(new MenuBar("MyVideoWindowMenubar"));
  videoPlayer->add(videoTitleBar);

  imageWindow->add(imageTitleBar);

  // Composite을 Composite의 자식으로 구성
  mainWindow->add(videoPlayer);
  mainWindow->add(imageWindow);

  mainWindow->printElementList();

  mainWindow->remove();

  mainWindow->printElementList();

...

```

## Related Pattern

---

구성요소-부모 간의 연결은 *Chain of Reponsibility Pattern*에서 많이 사용되는 예 이다.

*Decorator Pattern*은 자주 *Composite Pattern*과 함께 사용된다. 이 두 패턴이 함께 사용될 때는 둘 다 동일한 하나의 부모 클래스를 상속 받게 된다. 따라서 *Decorator*는 *Add()*, *Remove()*, *GetChild()* 와 같은 연산을 통해 *Component*의 인터페이스를 지원해야 한다.

*Flyweight Pattern* 으로 구성요소의 공유 방법을 알 수 있다. 단 공유되는 구성요소의 부모는 참조할 수 없다.

*Iterator Pattern*을 이용하면, 구성요소를 순회하는 방법을 얻을 수 있다.

*Visitor Pattern*을 이용하면, 이 패턴을 사용하지 않을 때, *Composite*와 *Leaf* 클래스에 걸쳐 분산될 수 있는 행동을 국소화시킬 수 있다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
