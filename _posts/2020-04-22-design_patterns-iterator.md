---
title: Design Patterns - Iterator
date: 2020-04-22 21:40:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Iterator
- C++
---

# Behavioral Patterns - Iterator

## Intent

---

내부 표현부를 노출하지 않고 어떤  집합 객체에 속한 원소들을 순차적으로 접근할수 있는 방법을 제공한다.

커서(Cursor)라고도 불린다.

## Utility

---

- 객체 내부 표현 방식으로 모르고도 집합 객체의 각 원소들에 접근하고 싶을 떄
- 집합 객체를 순회하는 다양한 방법을 지원하고 싶을 떄
- 서로 다른 집합 객체 구조에 대해서도 동일한 방법으로 순회하고 싶을 떄

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/iterator.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/itera039.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/itera040.gif)

### Elements

- **Iterator**
  - 원소를 접근하고 순회하는 데 필요한 언터페이스를 정의
- **ConcreteIterator**
  - *Iterator*에 정의된 인터페이스를 구현
  - 순회 과정 중 집합 객체 내에서 현재 위치를 기억
- **Aggregate**
  - *Iterator* 객체를 생성하는 인터페이스 정의
- **ConcreteAggregate**
  - 해당하는 *ConcreteIterator*의 인스턴스를 반환하는 *Iterator* 생성 인터페이스 구현

*ConcreteIterator*는 집합 객체 내 현재 객체를 계속 추적하고 다음번 방문할 객체를 결정한다.

## Feature

---

#### 집합 객체의 다양한 순회방법을 제공한다

구조가 복잡한 집합 객체는 다양한 방법으로 순회할 수 있다.

새로운 순회 방법을 *Iterator* 서브클래스로 정의하여 기존 순회 방법을 다른 순회 알고리즘 인스턴스로 완전히 교체할 수 있다.

#### *Iterator*는 *Aggregate* 클래스의 인터페이스를 단순화한다

*Iterator*의 순회 인터페이스는 *Aggregate* 클래스에 정의한 자신과 비슷한 인터페이스들을 없애서 *Aggregate* 인터페이스를 단순화할 수 있다..

#### 집합 객체에 따라 하나 이상의 순회 방법이 제공될 수 있다

각 *Iterator*마다 자신의 순회 상태가 있으므로 하나의 집합 객체를 한번에 여러번 순회 시킬수 있다.

## Implementation

---

### Considerations point in implementaion

#### 반복의 제어 주체

어떤 부분에서 반복을 제어할 것인지 결정하는 것이 가장 중요한 안건이다.

사용자가 반복을 제어할 때, 이 *Iterator*는 **외부반복자** 라고 한다. 반대로 *Iterator* 자신이 제어를 담당한다면 이는 **내부반복자** 라고 한다.

*외부반복자*를 사용하는 사용자 프로그램은 순회를 계속하고 다음번 원소를 명시적으로 해당 반복자에게 요청해야 한다.

사용자가 *내부반복자*를 사용한다면, 처리할 연산을 *내부반복자*에게 넘겨주고, 해당 반복자는 그 연산을 모든 원소에 적용한다.

*외부반복자*가 *내부반복자*보다는 유연한 방법이다. *외부반복자*를 이용하면 두 집합 객체가 동일한 집합인지 비교할 수 있지만, *내부반복자*를 이용할 때는 불가능하다.

#### 순회 알고리즘의 정의 위치

*Iterator* 클래스에만 순회 알고리즘을 정의할 수 있는 것은 아니다. *Aggregate* 클래스에도 순회 알고리즘을 정의하고, *Iterator*에는 순회의 상태만 저장할 수도 있다. 이렇게 구현된 *Iterator*를 가리켜서 **커서(Cursor)** 라고 한다. 단순히 집합 구조 내 현재 위치를 가리키기만 하는 것이다. 즉, 집합 객체의 현재 위치만 관리하도록 하고, 사용자는 *Next()* 연산을 호출하여 현재 위치 정보응 얻고 *Cursor*의 위치, 즉 *Cursor*의 상태를 변경한다.

만약 *Iterator*가 순회 알고리즘을 책임진다면 같은 집합 객체에 대해서 다른 순회 알고리즘을 구현하는 것이 쉬워지게 되고, 서로 다른 집합 객체에 동일한 순회 알고리즘을 재사용하는 것도 가능하다. 그러나, 이와같이 구현하기위해 *Iterator*가 집합 객체에 정의된 private 변수들에 접근해야 할 필요가 있을수 있게 되고, 이는 객체지향의 캡슐화 전략에 위배되게 된다.

#### *Iterator*를 견고하게 만드는 방안

집합 객체를 순회하는 동안 집합 객체를 수정하는 것은 위험한 일이다. 만약 순회 중에 새로운 값이 추가되거나 삭제된다면 동일한 원소를 두번 접근해야 할 수도 있고, 아니면 건너뛸 수도 있다.

**견고한 반복자(Robust Iterator)** 가 되려면 순회중에는 삽입이나 삭제가 일어나지 말아야 하고, 또 집합 객체를 복사하는 방법을 사용하지 않아야 한다.

대두분 *Robust Iterator*를 구현하기 위해 집합 객체에 *Iterator*를 등록하는 방식을 취한다. 삽입이나 삭제를 할 때, 집합 객체가 자신이 보유한 *Iterator*의 상태도 함께 변경할 수 있도록 하는 것이다.

#### 추가적으로 필요한 연산

*Iterator*에 필요한 최소한의 연산들은 *First()*, *Next()*, *IsDone()*, *CurrentItem()* 이다.

이외에도 순서가 정해진 집합 객체라면 *Previous()* 연산도 현재 *Iterator*의 위치를 앞으로 이동 시킬수 있으니 유용하게 쓰일수 있다. 또 인덱스를 가지는 집합 객체는 *SkipTo()* 연산을 통해, 어떤 조건에 일치하는 객체로 *Iterator*를 바로 이동시킬 수 있으므로 유용하게 쓰일수 있다.

#### C++에서의 다형성을 지닌 *Iterator*를 이용하는 방법

다형적인 *Iterator*는 런타임에 *Factory Method*로 동적으로 *Iterator* 객체를 제공해야 하기 때문에, 추가 비용을 지불해야 한다. 그렇기 때문에, 필요할 경우에만 다형성을 지닌 *Iterator*를 사용하고, 이외에는 확장된 *Iterator*를 사용하는것이 더 나은 일이다.

다형적인 *Iterator*의 단점 중 하나는 사용자가 직접 *Iterator*를 삭제하는 책임을 져야 한다는 것이다. 이떄, 오류가 발생할 수도 있는데, 이는 삭제하면서 객체에 할당된 힙 메모리를 삭제하지 않았기 때문이다. 즉, 어떤 예외가 발생하게 되면 생성된 *Iterator* 객체를 메모리에서 없애는 일을 전혀 진행할 수 없다.

*Proxy Pattern*을 쓰면 위와 같은 문제를 해결할 수 있는데, 실제로 스택에 저장한 *Proxy*를 *Iterator*처럼 사용하는 것이다. *Proxy*는 자신의 소멸자에서 *Proxy*를 삭제하면 된다. 이로써 *Proxy* 자신이 메모리 정리의 책임을 가지게 된다.

#### *Iterator*의 특수한 접근 권한

*Iterator*는 그 *Iterator*를 생성한 집합 객체의 확장으로 바라볼 수도 있는데, 이렇게 되면 *Iterator*와 집합 객체 간의 결합도가 매우 커지게 된다. C++ 에서 이러한 밀접한 구조를 구현한다면, 집합 객체를 정의할 때의 *Iterator*를 그 집합 구조의 *friend*로 정의할 수 있다. 이렇게 되면 단순하게 집합 객체에 접근하는데 필요한 연산들을 정의할 필요가 없다.

하지만 이렇게 특권적인 접근을 부여하면 새로운 순회 방법을 정의하기가 어려워진다. *friend*를 하나 더 추가하는 데에 집합 객체의 인터페이스 변경이 요구되기 때문이다. 

이런 문제를 해결하기 위한 방법은 집합 객체에 정의된 멤버 변수 중에서 중요하기는 하지만 공개할 수 없는 멤버 변수에 접근하는 연산을 *Iterator* 클래스 안에서 *protected*로 정의하는 것이다. 이렇게 되면 *Iterator*를 상속하는 서브클래스들은 이 연산을 통해 집합 객체에 접근할 수 있는 특권을 부여받고, 다른 사용자들은 집합 객체를 마음대로 사용할 수 없도록 제한된다.

#### *Composite*을 위한 *Iterator*

*외부반복자*는 *Composite Pattern*처럼 재귀적 합성 구조를 처리하도록 구현하기 까다롭다. 이런 구조에서는 현재 위치가 중첩된 집합체의 여러 단계에 걸쳐있기 때문이다. 이때는 *Composite Pattern*을 이용하여 자신이 거쳐온 단계에 대한 정보를 저장해 두어야 한다.

순회 정보를 자체적으로 관리하는 *내부반복자*는 자기 스스로가 재귀적 호출로 현재 위치를 저장하게 된다.

#### *Null Iterator*

*Null Iterator*는 영역판단을 하는데 유용하다. *Null Iterator*는 항상 순회 시에 끝나는 *Iterator*로 정의된다.

### Implement Example

```cpp
/**
* @brief Aggregate - AbstractList
* @details Iterator를 생성하는 Factory Method를 정의한 기반 클래스 
*/
template <class Item>
class AbstractList {
public:
  virtual Iterator<Item>* createIterator() const = 0;

  ...

};

/**
* @brief ConcreteAggregate - List
* @details 기반클래스를 상속하여 인터페이스 구현
*/
template <class Item>
class List : public AbstractList {
public:
  List(int size = DEFAULT_SIZE);

  int count() const;
  Item& getItem() const;

  Iterator<Item>* createIterator() const override {
    return new ListIterator<Item>(this);
  }

  ...

};

/**
* @brief Iterator - Iterator
* @details Iterator 인터페이스 정의
*/
template <class Item>
class Iterator {
public:
  // 인덱스를 첫번째 원소를 가리키도록 함
  virtual void first() = 0;
  // 인덱스를 다름 원소를 가리키도록 함
  virtual void next() = 0;
  // 리스트 내 다음 원소를 참조하는 인덱스의 유무를 확인
  virtual bool isDone() const = 0;
  // 현재 인덱스의 원소를 반환
  virtual Item currentItem() const = 0;

protected:
  Iterator();
};


/**
* @brief ConcreteIterator - ListIterator
* @details 자신만의 순회 알고리즘대로 Iterator 인터페이스 구현
*/
template <class Item>
class ListIterator : public Iterator<List> {
public:
  ListIterator(const List<Item>* list) : m_list(list), m_current(0) {}

  void first() override { m_current = 0; }

  void next() override { ++m_current; }

  bool isDone() const override { return m_current >= m_list->count(); }

  Item currentItem() const override {
    if(isDone())
      throw OutOfRange;

    return m_list->get(m_current);
  };

private:
  const List<Item>* m_list;
  int m_current;
};

/**
* @brief 내부반복자 - ListTraverser
* @details 내부반복자 인터페이스 정의
*/
template <class Item>
class ListTraverser {
public:
  ListTraverser(List<Item>* list) : m_iter(list) {}

  // 내부적으로 순회
  bool Traverse() {
    bool result = false;

    // Iterator를 통해 Aggragaet 순회
    for(m_iter.first() ; !m_iter.isDone() ; m_iter.next()) {

      // 원소가 있으면 true, 없으면 false를 반환
      result = processItem(m_iter.currentItem());

      if(result == false)
        break;
    }

    return result;
  }

protected:
  virtual bool ProcessItem(const Item&) = 0;

private:
  ListIterator<Item> m_iter;
};

/**
* @brief 내부반복자 - ListTraverser
* @details 내부반복자 인터페이스 정의
*/
template <class Item>
class ViewButtons : public ListTraverser<Button> {
public:
  ButtonList(List<Button*>* list, int n) : m_iter(list),
                                           m_total(n),
                                           m_count(0) {}

protected:
  bool processItem(Button* const btn) {
    ++m_count;

    btn->show();

    return m_count < m_total;
  }

private:
  int m_total;
  int m_count;
};

...

  // Aggregate 객체 생성
  AbstractList<Button*>* btnList;

  // 생성한 Aggregate 클래스에 대한 Iterator 객체 생성
  Iterator<Button*> iter = btnList->createIterator();

  // Iterator를 통한 순회
  for(iter.first() ; !iter.isDone() ; iter.next() ){

    ...

  }

  delete iter;

  ...

  // 내부반복자로 순회하기 위한 Aggregate 객체 생성
  List<Button*>* btns;

  // 내부반복자 객체 생성
  ViewButtons viewBtns(btns, 10);
  
  // 내부반복자를 통한 순회
  viewBtns.Traverse();

  ...

...

```

## Related Pattern

---

*Iterator Patten*은 *Composite Patten*과 같이 재귀적 구조가 있을 때 자주 사용한다.

다양한 *Iterator*를 사용하여 적당한 *Iterator* 클래스를 얻으려면 *Factory Method Pattern*을 사용할 수 있다.

*Mementor Pattern*도 *Iterator Pattern*과 함께 자주 사용하는데, 이때 *Iterator* 자신이 반복한 결과를 저장하기 위해서 *Memento*를 사용한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
