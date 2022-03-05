---
title: Design Patterns - Visitor
date: 2020-04-27 21:32:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Visitor
- C++
---

# Behavioral Patterns - Visitor

## Intent

---

객체의 구조와 기능을 분리시킨다.

구조는 변하지 않으며 기능만 따로 추거되거나 확장될 경우 사용할 수 있는 패턴이다.

객체 구조를 이루는 원소에 대해 수행할 연산을 표현한다.

연산을 적용할 원소의 클래스를 변경하지 않고도 새로운 연산을 정의할 수 있게 한다.

## Utility

---

- 다른 인터페이스를 가진 클래스가 객체 구조에 포함되어 있어며, 구체 클래스에 따라 달라진 연산을 이들 클래스의 객체에 대해 수행하고자 할 때
- 각각 트깅이 있고, 관련되지 않은 연산들이 한 객체 구조에 속해있는 객체들에 대해 수행될 필요가 있으며, 연산으로 클래스들을 "더럽히고" 싶지 않을 때
  - *Visitor* 클래스는 관련된 모든 연산을 하나의 클래스 안에다 정의해 놓음으로써 관련된 연산이 함께 있을수 있게 해 준다.
  - 어떤 객체 구조가 많은 응용프로그램으로 공유돌 때, *Visitor* 클래스를 이용하면 이 객체 구조가 필요한 응용프로그램에만 연산을 둘 수 있다.
- 객체 구조를 정의한 클래스는 거의 변하지 않지만, 전체 구조에 걸쳐 새로운 연산을 추가하고 싶을 떄
  - 객체 구조를 변경하려면 모든 방문자에 대한 인터페이스를 재정의해야 하는데, 이 작업에 잠재된 비용이 클 수 있다.
  - 객체 구조가 자주 변경될 때는 해당 연산을 클래스에 정의하는 편이 더 낫다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/visitor.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/visit006.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/visit113.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/visit112.gif)

### Elements

- **Visitor**
  - *NodeVisitor*에 해당
  - 객체 구조 내에 있는 각 *ConcreteElement* 클래스를 위한 *Visit()* 연산을 선언
  - 연산의 이름과 인터페이스 형태를 통해, *Visit()* 요청을 방문자에게 보내는 클래스를 식별
  - 방문자는 방문된 원소의 구체 클래스를 결정 가능
  - 방문자는 원소가 제공하는 인터페이스를 통해 원소에 직접 접근 가능
- **ConcreteVisitor**
  - *TypeCheckingVisitor*에 해당
  - *Visitor* 클래스에 선언된 연산을 구현
  - 각 연산은 구조 내에 있는 객체의 대응 클래스에 정의된 일부 알고리즘을 구현
  - *ConcreteVisitor* 클래스는 알고리즘이 운영될 수 있는 상황 정보를 제공하며 자체 상태를 저장
- **Element**
  - *Node*에 해당
  - 방문자를 인자로 받아들이는 *Accept()* 연산 정의
- **ConcreteElement**
  - *AssignmentNode*, *VariableRefNode*에 해당
  - 인자로 방문자 객체를 받아들이는 *Accept()* 연산 구현
- **ObjectStructure**
  - *Program*에 해당
  - 객체 구조 내의 원소들을 나옇
  - 방문자가 원소에 접근하게 하는 상위 수준 인터페이스를 제공
  - *ObjectStructure*는 *Composite Pattern*으로 만든 복함체 일 수도 있고, 리스트나 집합등 컬렉션으로 구현 가능

#### Elements Communication

*Visitor Pattern*을 사용하는 사용자는 *ConcreteVisitor* 클래스의 객체를 생성하고 객체 구조를 따라서 각 원소를 방문하여 순회해야 한다.

방문자가 구성 원소들을 방문할 때, 구성 원소는 해당 클래스의 *Visitor* 연산을 호출한다. 이 원소들은 자신을 *Visitor* 연산에 필요한 인자로 제공하여 방문자 자신의 상태에 접근할 수 있도록 한다.

다음의 상호작용 다이어그램은 방문자 객체와 두 원소 사이의 협력 관계를 보여준다.

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/visit003.gif)

## Feature

---

#### *Visitor* 클래스는 새로운 연산을 쉽게 추가한다

*Visitor* 클래스는 복잡한 객체를 구성하는 요소에 속한 연산을 쉽게 추가할 수 있다. 새로운 방문자를 추가하면 객체 구조에 대한 새로운 연산을 추가할 것이 된다.

#### 방문자를 통해 관련된 연산들을 한 군데로 모으고 관련되지 않은 연산을 떼어낼 수 있다

관련된 행동들이 객체 구조를 정의하는 클래스에 분산되지 않게되며, 방문자 클래스에 모인다. 관련되지 않은 행동들은 그 자신의 방문자 서브클래스로 나뉜다.

이는 원소를 정의하는 클래스 및 방문자에 정의되어 있는 알고리즘 모두를 간단하게 만든다. 알고리즘에 특화된 자료 구존는 어떤 것이든지 방문자 속에 숨겨진다.

#### 새로운 *ConcreteElement* 클래스를 추가하기 어렵다

*Visitor Pattern*을 사용하면 *Element* 클래세에 대한 새로운 서브클래스를 추가하기 어려워 진다.

*ConcreteElement* 클래스가 새로 생길때 마다, *Visitor* 클래스에 대한 새로운 추상 연산 및 모든 *ConcreteVisitor* 클래스에 그 연산에 대응하는 구현을 제공해야 한다. *Visitor* 클래스에 기본적인 구현이 간혹 제공되기는 하지만, 일반적인 상황은 아니다.

*Visitor Pattern*을 적용할 때 중요하개 고려해야 하는 사항은, 객체의 구조에 적용될 알고리즘의 변화가 자주 발생하는가, 아니면 이 같은 구조를 구성하는 객체의 클래스의 변화가 자주 발생하는가 이다.

*Visitor* 클래스 계통은 새로운 *ConcreteElement* 클래스가 자주 추가되는 상활에서는 유지하기가 상당히 까다롭다.

#### 클래스 계층 구조에 겅쳐서 방문한다

*Iterator*는 객체 구조 내 원소를 순회하면서 그 원소의 연산을 호출하여 객체 방문을 수행할 수 있다. 그러나 서로다른 타입의 원소를 가지는 객체 구조를 오가면서 순회를 진행할 수는 없다.

```cpp
template<class Item>
class Iterator {
  
  ...

  Item CurrentItem() const;
};
```

즉, *Iterator*가 방문할 수 있는 모든 원소에는 공통적으로 *Item* 부모 클래스가 있다는 뜻이다.

방문자는 위와같은 *Iterator*의 제약을 가지지 않는다. 방문자는 동일한 부모 클래스가 없는 객체들도 방문 가능하다. *Visitor* 인터페이스에 어떤 객체의 타입이라도 추가할 수 있다.

```cpp
class Visitor {
public:
  
  ...

  void VisitMyType(MyType*);
  void VisitYourType(YourType*);
};
```

*MyType* 및 *YourType*은 상속 등을 통해 관련될 필요가 전혀 없다.

#### 상태를 누적할 수 있다

방문자는 객체 구조 내 각 원소들을 방문하면서 샅태를 누적 할 수 있다. 만일 방문자가 없다면, 이 상태는 별도의 다른 인자로서 순회를 담당하는 연산에 전달되든지, 아니면 전역 변수로 존재해야 할 것이다.

#### 데이터 은닉을 깰 수 있다

*Visitor Pattern*은 *ConcreteElement* 인터페이스가 방문자에게 필요한 작업을 수행시킬만큼 충분히 강력하다는 가정을 깔고간다.즉, *Visitor Pattern*을 쓰면 개발자는 원소의 내부 상태에 접근하는 데 필요한 연산들을 모두 공개 인터페이스로 만들 수 밖에 없는데, 이는 캡슐화전략을 위배하는 것이다.

## Implementation

---

### Considerations point in implementaion

일반적으로 *Visitor* 클래스, *Element* 클래스, *ConcreteElement* 클래스, *CompositeElement* 클래스는 C++에서 아래와 같이 구현된다.

```cpp
class Visitor {
public:
  virtual void VisitElementA(ElementA*);
  virtual void VisitElementB(ElementB*);

  // 다른 구체연 요소들에 대한 visitor 연산

  ...


private:
  Visitor();
};

class Element {
public:
  virtual ~Element();
  virtual void Accept(Visitor&) = 0;

protected:
  Element();
}

class ElementA : public Element {
public:
  ElementA();
  void Accept(Visitor& v) override { v.VisitorElementA(this); }
};

class CompositeElement : public Element {
public:
  void Accept(Visitor& v) override {
    std::vector<Element*> i(m_child);

    for(const auto& iter : i) {
      iter->Accept(v);
    }

    v.VisitCompositeElement(this);
  }

private:
  std::vector<Element*> m_child;
};
```

*Visitor Pattern*을 적용할 때 생기는 구현 이슈 두가지에 대하여 아래와 같이 정리될 수 있다.  

#### 이중 디스패치

실질적으로 따지고보면, *Visitor Pattern*은 사용자가 클래스를 변경하지 않고 연산을 클래스에 추가하도록 만드는 패턴이다. 이를 위해 **이중 디스패치(Double Dispatch)** 라는 기법을 사용한다.

C++의 경우 **단일 디스패치(Single Dispatch)**를 지원한다. *단일 디스패치* 언어에서 어떤 연산이 요청을 처리할 것인지 결정하는 기준은 두가지 이다.

- 요청의 이름
- 수신자의 타입

예를들어, C++에서 *VariableRefNode* 인스턴스에 대해 *GernerateCode*를 호출하는 것은 *VariableRefNode::GernerateCode()* 를 호출하는 것이 된다. 즉, 실행되는 연산은 요청의 종류과 수신자의 타입에 따라 달라지게 되는 것이다.

*이중 디스패치*는 실행되는 연산이 요청의 종류와 두 수신자의 타입에 따라 달라진다는 뜻이다. 위 예의 *Accept()* 연산은 이중 디스패치 연산으로, *Visitor*의 타입과 *Element*의 타입에 따라 달라지게 된다. 즉, 이중 디스패치를 쓰면 방문자는 원소의 각 클래스에 대해 서로 다른 연사늘 요청할 수 있다.

실제로 실행되는 연산은 *Visitor*의 타입과 그것이 방문하는 *Element*의 타입에 따라 달라진다는 점이 *Visitor Pattern*의 주요 핵심이다. 연산과 *Element* 인터페이스를 정적으로 바인딩하는 대신에, *Visitor* 내 연산들을 한 군데로 모으고 *Accept()* 연산을 사용해서 런타임에 바인딩을 진행 할 수 있다.

*Element* 인터페이스를 확장하는 작업은 새로운 *Element* 서브클래스를 많이 정의하게 되는 것이 아니라, 하나의 방문자 서브클래스를 정의하는 정도의 작업으로 단순해진다.

#### 객체구조 순회의 책임

방문자는 각 객체 구조 요소에 방문해야 한다.

순회를 위한 연산은 세곳 중 어떤곳이라도 놓일 수 있다.

- 객체 구조
  - 컬렉션은 *Accept()* 연산을 호출함으로써 자신의 원소들을 순회
  - 복합체는 각 *Accept()* 연산이 그 원소의 자식을 순회하고 다시 자식들에 대해 재귀적으로 *Accept()* 를 호출함으로써 순회를 진행
- 방문자
  - *ConcreteElement*에 대한 *ConcreteVisitor* 마다 동일한 순회 알고리즘 코드를 중복시켜야 함
  - 객체 구조에 대한 연산 호출의 결과에 따라 다른 복잡한 순회 방법을 구현 가능
- 별도의 *Iterator* 객체
  - 내부 반복자, 외부 반복자를 통해 원소들을 순회



### Implement Example

```cpp
/**
* @brief  Element - Equiment
* @details
*/
class Equiment {
public:
  virtual ~Equiment();

  const char* name() { return m_name; }

  virtual Watt power();
  virtual Currency netPrice();
  virtual Currency discountPrice();

  virtual void accept(EquimentVisitor&);

protected:
  Equiment(const char*);

private:
  const char* m_name;
};

/**
* @brief  Visitor - EquimentVisitor
* @details 
*/
class EquimentVisitor {
public:
  virtual ~EquimentVisotor();

  virtual void VisitFloppyDisk(FloppyDisk*);
  virtual void VisitCard(Card*);
  virtual void VisitChassis(Chassis*);
  virtual void VisitBus(Bus*);

  // Equiment의 구체 서브클래스를 위한 기타 연산들

  ...

protected:
  EquimenVisitor();
};

/**
* @brief  ConcreteElement - FloppyDisk
* @details 
*/
class FloppyDisk : public Equiment {
public:
  FloppyDisk();

  void accept(EquimentVisitor& visitor) override {
    visitor.VisitFloppyDisk(this);
  }

  ...

};

/**
* @brief  ConcreteElement - Chassis
* @details
*/
class Chassis : public Equiment {
public:
  Chassis();

  void accept(EquimentVisitor& visitor) override {
    for(const auto& iter : m_equimentVec){
      iter.accept(visitor);
    }

    visitor.VisitChassis(this);
  }

  ...

private:
  std::vector<Equiment*> m_equimentVec;
};


/**
* @brief  ConcreteVisitor - Equiment
* @details
*/
class PricingVisitor : public EquimentVisitor {
public:
  PricingVisitor();

  Currency& getTotalPrice();

  void VisitFloppyDisk(FloppyDisk* e) override { m_total += e->netPrice(); }
  void VisitCard(Card* e) override { ... }
  void VisitChassis(Chassis* e) override { m_total += e->discountPrice(); }
  void VisitBus(Bus* e) override { ... }

  ...

private:
  Currency m_total;
};

/**
* @brief  ConcreteVisitor - Equiment
* @details
*/
class InventoryVisitor : public EquimentVisitor {
public:
  InventoryVisitor();

  Inventory& getInventory();

  void VisitFloppyDisk(FloppyDisk* e) override { m_inventory.push(e); }
  void VisitCard(Card* e) override { ... }
  void VisitChassis(Chassis* e) override { m_inventory.push(e); }
  void VisitBus(Bus* e) override { ... }

  ...

private:
  Inventory m_inventory;
};


...

  Equiment* component;
  InventoryVisitor visitor;

  component->accept(visitor);
  std::cout << "Inventory" << std::endl;
  std::cout << "Name : " << component->name() << std::endl;
  std::cout << "Inventory List : " << visitor.getInventory() << std::endl;
  

...

```

## Related Pattern

---

*Composite Pattern*이 정의하는 복합 객체 구조에 대해 연산을 적용하는 데 *Visitor Pattern*를 사용할 수 있다. *Visitor Pattern*은 *Interpreter Pattern*의 해석과정에도 사용 가능하다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
