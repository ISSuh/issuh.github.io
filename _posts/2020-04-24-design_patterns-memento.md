---
title: Design Patterns - Memento
date: 2020-04-24 15:34:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- Memento
- C++
---

# Behavioral Patterns - Memento

## Intent

---

캡슐화를 위배하지 않은 채 어떤 객체의 내부상태를 잡아내고 실체화시켜 둠으로써, 이후 해당 객체가 그 상태로 되돌아올수 있도록 한다.

토큰(Tocken)이라고도 불린다.

## Utility

---

- 어떤 객체의 상태에 대한 스냅샷(몇개의 일부)을 저장한 후 나중에 이 상태로 복구해야 할 때
- 상태를 얻는 데 필요한 직접적인 인터페이스를 두면 그 객체의 구현 세부사항이 드러날 수 밖에 없고, 이것으로 객체의 캡슐화가 깨질 때

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/memento.gif)

### Elements

- **Memento**
  - 원조본 객체의 내부 상태를 저장
  - *Memento*는 *Originator* 객체의 내부상태를 필요한 만큼 저장해 둠
  - 클래스 내부에는 크게 두 종류의 인터페이스가 존재
    - **제한 범위(narrow) 인터페이스** : 관리 책임을 가지는 *Caretaker* 클래스가 접근
    - **광범위(wide) 인터페이스** : 자신의 상태를 이전 상태로 복원하기 위해 필요한 모든 자료에 접근해야하는 *Originator* 클래스가 접근
  - 이상적으로는 *Memento*를 생성하는 *Originator* 객체만이 *Memento*의 내부 상태에 접근할수 있는 권한을 가짐
- **Originator**
  - 원조본 객체
  - *Memento*를 생성하여 현재 객체의 상태를 저장하고 *Memento*를 사용하여 내부 상태를 복원
- **Caretaker**
  - *Memento*의 보관을 책임지는 보관자
  - *Memento*의 내용을 검사
  - *Memento*의 내용을 건드리지는 않음

*Caretaker* 객체는 원조본 객체에 *Memento* 객체를 요청한다. 또 요청한 시간을 저장하며, 받은 *Memento* 객체를 다시 원조본 객체에게 돌려주는데, 이를 상호작용 다이어그램으로 표현하면 다음과 같다.

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/memen027.gif)

*Caretaker* 객체는 *Memento* 객체를 *Originator* 객체에 전달하지 않을 수 있다. *Originator* 객체가 이전 상태로 돌아갈 필요가 없을 때는 전달할 필요가 없기 때문이다.

*Memento* 객체는 수동적이다. *Memento* 객체를 생성한 *Originator* 객체만이 상태를 설정하고 읽을 수 있다.

## Feature

---

#### 캡슐화된 경계를 유지할 수 있다

*Originator*만이 *Memento*를 다룰수 있기 때문에 *Memento*가 외부에 노출되지 않는다.

*Memento Patern*은 복잡한 *Originator* 클래스의 내부 상태를 다른 객체로 분리하는 방법으로 상태에 대한 정보의 캡슐화를 보장한다.

#### *Originator* 클래스를 단순화 할 수 있다

사용자가 자신들이 필요한 상태를 별도로 관리하게 되면 *Originator* 클래스는 간단해지고, 상태를 변경할 때마다 사용자가 이를 *Originator*에 알려줄 필요도 없다.

#### *Memento*의 사용으로 더 많은 비용이 들어갈 수 있다

*Originator* 클래스가 많은 양의 정보를 저장해야 할 떄나 상당히 자주 *Memento*를 반환해야 할 때라면, *Memento*가 상당한 오버헤드를 유발할 수 있다.

*Originator* 클래스의 상태를 보호하는 비용과 상태 복구의 비용이 싸지 않으면, *Memento Pattern*은 적합하지 않다.

#### 제한 범위 인터페이스와 광범위 인터페이스를 정의해야 한다

어떤 프로그래밍 언어에서는 *Originator* 객체만 *Memento*의 상태에 접근할 수 있도록 보장하기가 어려울 수 있다.

#### *Memento*를 관리하는 데 필요한 비용이 숨어있다

*Caretaker* 객체는 자신이 보관하는 *Memento*를 삭제할 책임이 있다. 그러나 *Caretaker* 쪽에서는 얼마나 많은 상태가 *Memento*에 저장되어 있는지 알 방법이 없다. 그러므로 *Caretaker* 객체가 아무리 가볍다 해도 *Memento*를 저장할 때 적지 않은 저장 비용을 유발할 수 있다.

## Implementation

---

### Considerations point in implementaion

#### 언어의 지원 여부

*Memento*에는 두가지 종류의 인터페이스가 있다.

- 제한 범위 인터페이스
  - 다른 객체들에게 제공할 서비스를 정의
- 광범위 인터페이스
  - *Originator* 클래스에게 제공하는 서비스를 정의

C++에서는 *originator* 클래스를 *Memento* 클래스의 프랜드 클래스로 정의하고, *Memento* 클래스의 광범위 인터페이스를 모두 private 멤버로 만든다. 제한 범위 인터페이스에 해당하는 연산만 public으로 정의해야 한다. 즉, *Originator* 클래스는 *Memento* 클래스의 프랜드 클래스이므로, 모든 연산에 접근할 수 있어 광범위 언터페이스를 제공하는 셈이 된다. 그러나 이외의 클래스들은 public으로 정의된 연산만을 사용할 수 있으므로 제한 범위 인터페이스를 제공하는 셈이 된다.

```cpp
// 내부 상태를 표현하는 클래스
class State;

class Originator{
public:
  Memento* createMemento();
  void setMemento(const Memento*);
  
  ...

private:
  State* m_state;

  ...

};

class Memento {
public:
  virtual ~Memento();

  // 제한 범위 인터페이스 정의

  ...

private:
  // Originator 클래스만 접근가능한 광범위 인터페이스 정의
  friend class Originator;

  Memento();
  void setState(State*);
  State* getState();

  ...

private:
  State* m_state;

  ...

};

```

#### 점즘적 상태 변경을 저장

*Memento*가 생성되어 다시 *Originator*에게 반환되면 *Memento*는 *Originator*의 내부 상태 변경 과정을 지속적으로 저장해야 한다. 이떄, 모든 상태를 다 저장하는 것이 아니라 변경된 정보들만 계속 추가해 간다.

### Implement Example

```cpp
class Graphic;

/**
* @brief Originator - ConstraintSolver
* @details 상태 정보를 기록해야할 Originator 클래스
*/
class ConstraintSolver() {
public:
  static ConstraintSolver* Instence();

  void addConstraint(Graphic* startConnection,
                     Graphic* endConnection);

  void removeConstraint(Graphic* startConnection,
                        Graphic* endConnection);

  // addConstraint() 연산으로 등록한 제약상황들을 처리
  void solve();

  ConstraintSolverMemento* createMemento();
  void setMemento(ConstraintSolverMemento* );

private:
  // 연결을 처리하기 위해 필요한 상태나 연산을 정의

  ...

}

/**
* @brief Memento - ConstraintSolverMemento
* @details Originator 객체의 상태를 저장하고 이를 반환하는 Memento 클래스
*/
class ConstraintSolverMemento {
public:

  // 다른 클래스들에게 공개할 제한 범위 인터페이스 정의
  virtual ~ConstraintSolverMemento();

  ...

private:
  // Originator 클래스들에게만 공개할 광범위 인터페이스 정의
  friend class ConstraintSolver;
  
  ConstraintSolverMemento();

  ...

};

/**
* @brief Caretaker - MoveCommand
* @details Memento 객체륿 보관하며 관리하는 Caretaker 클래스
*/
class MoveCommand() {
public:
  MoveCommand(Graphic* target, const Point& delta);

  void excute() {
    ConstraintSolver* solver = ConstraintSolver::instence();

    // 현재상태를 저장한 Memento 생성
    m_state = solver->createMemento();

    m_target->move(m_delta);

    solver->solve();
  }

  void unExcute() {
    ConstraintSolver* solver = ConstraintSolver::instence();
    m_target->move(m_delta);

    // 상태 복구
    solver->setMemento(m_state);

    solver->solve();
  }

private:
  ConstraintSolverMemento* m_state;
  Point m_delta;
  Graphic* m_target;
};

```

## Related Pattern

---

*Command Pattern*은 실행 취소가 가능한 연산의 상태를 저장할 떄 *Memento Pattern*을 사용할 수 있다.

*Memento Pattern*은 *Iterator Pattern*에서의 반복 과정 상태를 관리할 수 있다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
