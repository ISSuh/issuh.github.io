---
title: Design Patterns - State
date: 2020-04-25 12:12:00
categories:
- Design Patterns
tags:
- Design Patterns
- Behavioral Patterns 
- State
- C++
---

# Behavioral Patterns - State

## Intent

---

객체 내부 상태에 따라 스스로 행동을 변경할 수 있개 허가하는 패턴으로, 이렇게 하면 객체는 마치 자신의 클래스를 바꾸는 것과 같은 효과를 보인다.

상태 표현 객체(*Object for State*) 라고도 불린다.

## Utility

---

- 객체의 행동이 상태에 따라 달라질 수 있고, 객체의 상태에 따라서 런타임에 행동이 바뀌어야 할 때
- 어떤 연산안에 그 객체의 상태에 따라 달라지는 다중 분기 조건 처리가 너무 많이 들어있을 때
  - 객체의 상태를 표현하기 위해 상태를 하나 이상의 나열형 상수(enum)으로 정의해야 한다.
- 동일한 조건 문장들을 하나 이상의 연산에 중복 정의해야 할 때
  - 객체의 상태를 별도의 객체로 정의하면, 다른 객체들과 상관없이 그 객체의 상태를 다양화 할 수 있다.  

## Structure

---

### Basic Structure

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/state.gif)

### Structure Example

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/state012.gif)

![example](http://www.cs.unc.edu/~stotts/GOF/hires/Pictures/state-eg.gif)

### Elements

- **Context**
  - *TCPConnection*에 해당
  - 사용자가 관심 있는 인터페이스를 정의
  - 객체의 현재 상태를 정의한 *ConcreteState* 서브클래스의 인스턴스를 유지, 관리
- **State**
  - *TCPState*에 해당
  - *Context*의 각 상태별로 필요한 행동을 캡슐화하여 인터페이스로 정의
- **ConcreteState**
  - *TCPEstabilished*, *TCPListen*, *TCPClosed*에 해당
  - *State*의 각 서브클래스들은 *Context*의 상태에 따라 처리되어야 할 실제 행동을 구현

#### Elements Communication

상태에 따라 다른 요청을 받으면 *Context* 클래스는 현재의 *ConcreteState* 객체로 전달한다. 이 *ConcreteState* 클래스의 객체는 *State* 클래스를 상속하는 서브클래스들 중 하나의 인스턴스 이다.

*Context* 클래스는 실제 연산을 처리할 *State* 객체에 자신을 매개변수로 전달한다. 이로써 *State* 객체는 *Context* 클래스에 정의된 정보에 접근할 수 있게된다.

*Context* 클래스는 사용자가 사용할 수 있는 기본 인텀페이스를 제공한다. 사용자는 *State* 객체를 *Context* 객체와 연결시킨다. 즉, *Context* 클래스에 현재 상태를 정의하는 것이다. 이렇게 *Context* 객체를 만들고 나면 사용자는 더는 *State* 객체를 직접 다루지 않고 *Context* 객체에 요청을 보내기만 하면 된다.

*Context* 클래스 또는 *ConcreteState* 서브클래스들은 자기 다음의 상태가 무엇이고, 어떤 환경에서 다음 상태로 가는지 결정할 수 있다. 즉, 상태는 상태 전이 규칙이 있으므로 각각 한 상태에서 다른 상태로 전이하는 규칙을 알아야 한다.

## Feature

---

#### 상태에 따른 행동을 국소화하며, 서로 다른 상태에 대한 행동을 별도의 객체로 관리한다

*State Pattern*을 사용하면 임의의 한 상태에 관련된 모든 행동을 하나의 객체로 모을수 있다.

*State Pattern*을 사용하지 않는다면, 내부 상태를 저장해 둘 변수를 선언하고, *Context*의 연산이 그 데이터를 명시적으로 점검해야 한다. 즉, 이 변수를 통해 상태를 계속 확인하기 위해 case문을 통해 구현한다. 그러나 각 연산마다 태별 처리방법이 다르다면 동일한 case문이 여러 연산에 걸처 반복적으로 나타나게 되고, 새로운 상태가 추가될 떄마다 여러 연산을 변경하게 된다.

*State Patten*은 위와 같은 문제를 해결할 수 있다. 한 상태에 종속적인 코드를 *State* 클래스의 서브클래스에 모두 정의하기 때문에 새로운 상태와 새로운 전이 규칙이 발견되면 새로운 서브클래스만 정의하면 되기 때문이다. 그러나 이 패턴은 상태에 따른 행동을 여러 클래스에 나누어 정의하기 때문에 클래스의 수가 많아 질 수 있고, 단일 클래스보다 콤팩트하지 않다.

*State Pattern* 상태별 거대한 코드를 구조화하는데 도움을 준다. 상태 전이에 필요한 코드를 if-else문으로 처리하지 않고, 각 상태 클래스의 연산을 독립적으로 구현하여 처리한다.

#### 상태 전이를 명확하게 만든다

어떤 객체가 자신의 현재 상태를 오직 내부 데이터 값으로만 정의하면, 상태 전이는 명확한 표현을 가지지 못한다. 단지 이 상태 변수에 값을 항당하는 문장 정도밖에는 되지 않는다.

각 상태별로 별도의 객체를 만드는 것은 상태 전이를 명백하게 해주는 결과가 된다. 상태 전이가 *Context* 클래스의 관점과 상관없이 원자적이기 때문에, *State* 객체는 *Context* 객체가 일관되지 않은 상태가 되는 것을 막아줄 수 있다.

상태는 여러 개의 변수가 아니라 하나의 변수, 즉 *Context*의 *State* 객체 변수를 재바인딩함으로써 전이될 뿐이다.

#### 상태 객체는 공유될 수 있다

상태는 단지 타입으로만 표현되므로, *State* 객체는 인스턴스 변수 없이 여러 *Context* 클래스의 인스턴스로도 객체를 공유할 수 있다.

상태가 위와같이 공유될 때, 이 공유된 상태는 실질적으로 *Flyweight* 객체라고 해도 무방하다.

## Implementation

---

### Considerations point in implementaion

#### 상태 전이를 정하는 주체

*State Pattern*은 어떤 참여자가 상태 전이를 책임질지 명시하지 않는다.

상태 전이의 기준을 *Context* 클래스안에 구현할 수도 있고, *State* 클래스의 서브클래스들이 자신 다음에 오는 상태가 무엇이고 상태 전이가 언제 될지를 정할 수도 있다. 일반적으로 후자의 경우가 더 유연하고 적절한 방법이다.

상태 전이 코드가 여기저기 분산된다면, *State*의 새로운 서브클래스를 만드는 것이 더 쉬워진다. 그러나 *State* 서브클래스가 적어도 자신 다음에 나오는 다음 *State* 서브클래스를 알아야 하기 때문에 서브클래스 사이의 구현 종속성이 생길 수 있다.

#### 테이블 기반의 대안

테이블 기반의 대안은 입력 값과 상태 전이를 매핑하는 방법이다. 각 상태마다 테이블을 이용하여 그 상태에서 처리 가능한 입력과 그 입력으로 생길 수 있는 다음 상태를 매핑하는 것이다. 이 방법을 사용하면 조건식 코드(*State Patten*의 경우 가상함수도 포함된다)가 테이블 탐색 코드로 바뀌게 된다.

테이블을 이용하는 구현의 경우, 프로그램을 바꾸지 않고 테이블에 저장한 값만 바꾸면 새로운 전이 기준으로 바꿀수 있다는 장점이 있다. 즉, 테이블이라는 형식에 전이 기준을 맞춘다는 점에서 규칙성(Regularity)가 있다는 것이다.

그러나 아래와 같은 단점도 존재한다.

- 테이블 탐색은 간혹 가상함수 및 일반함수 호출보다 비효율적일 때가 있다.
- 상태 전이에 관한 논리 흐름을 테이블이라는 규칙적인인 형식에 맞추면 전이 기준이 불명확해지며 이해하지 어려워진다.
- 상태 전이에 따른 처리 동작을 추가하기가 복잡하다.
  - 테이블을 이용한 벙법은 상태와 전이를 테이블에 저장하여 어느 상태로 전이해야 하는지 알아야 하며, 각 전이를 위해 임의로 처리해야 하는 연산을 수행하려면 각각의 처리 내용도 추가해야 한다.

테이블 기반의 대안과 *State Pattern* 사이에 존재하는 가장 큰 차이는, *State Pattern* 주 목적은 상태별로 다른 행동을 별도의 클래스로 만드는 것인 반면, 테이블 기반의 방법은 상태 전이를 정의하는 쪽에 초점을 맞추는 것이다.

#### 상태 객체의 생성과 소멸

상태 객체의 생성과 소멸을 구현할 떄 가장 일반적인 선택사항은 아래 두가지가 있다.

- 상태 객체를 필요할 때만 생성하고 필요 없게 되면 없앤다.
  - 상태가 실행되기 전까지는 어떤 상태여야 하는지 모르거나 상황에 따라 상태가 자주 바뀌지 않을때 유용하다.
  - "사용하지 않는 객체는 생성하지 않는다"는 원칙으로, *State* 객체가 많은 정보를 가지고 있을때 유용하다.

- 필요하기 전에 미리 만들어 둔 후 없애지 않고 계속 둔다.
  - 필요할 떄마다 생성하고 다시 없애는 것을 반복할 필요가 없기 때문에, 상태 변화가 수시로 일어날 경우에 유용하다.
  - *Context* 클래스가 언제나 모든 상태에 대한 참조자를 계속 관리해야 하는 부담이 생긴다.

### Implement Example

```cpp
/**
* @brief Context - TCPConnection
* @details 사용자측에서 사용할 인터페이스 정의
*/
class TCPConnection {
public:
  TCPConnection() { 
    // TCPConnection의 초기상태를 TCPClosed으로 설정
    m_state = TCPClosed::Instance(); 
  }

  void processOctet(TCPOctetStream*);

  // State에 대한 Context의 행동의 인터페이스 정의
  // 행동에 대한 실제 구현은 ConcreteState에서 구현
  // 해당 함수가 실제로 동작하는 행동은 현재 State에 위임
  void activeOpen() { m_state->activeOpen(this); }
  void passiveOpen() {m_state->passiveOpen(this); }
  void close() { m_state->close(this); }
  void send(void* data) { m_state->send(data, this); }
  void acknowledge() { m_state->acknowledge(this); }
  void synchronize() { m_state->synchronize(this); }

protected:
  friend class TCPState;
  void changeState(TCPState* state) { m_state = state; }

private:
  TCPState* m_state;
};

/**
* @brief State - TCPState
* @details Context의 상태와 그 행동에 대한 인터페이스 정의
*/
class TCPState{
public:
  virtual ~State();

  virtual void transmit(TCPConnection* connection, tcpOctetStream* stream) {}
  virtual void activeOpen(TCPConnection* connection) {}
  virtual void passiveOpen(TCPConnection* connection) {}
  virtual void close(TCPConnection* connection) {}
  virtual void synchronize(TCPConnection* connection) {}
  virtual void acknoweldge(TCPConnection* connection) {}
  virtual void send(TCPConnection* connection) {}

protected:
  void changeState(TCPConnection* connection, TCPState* state) {
    // Context의 상태를 매개변수로 받은 ConcreteState로 변경
    connection->changeState(state);
  }
};

/**
* @brief ConcreteState - Established
* @details 해당하는 State의 인터페이스에 대해 행동을 구현
*/
class TCPEstablished : public TCPState {
  static TCPState* instence();

  void transmit(TCPConnection* connection, tcpOctetStream* stream) override {
    connection->processOctet(stream);
  }

  void close(TCPConnection* connection) override {
    // 행동 정의

    ...

    // TCPConnection의 상태를 TCPListen으로 상태 변경
    changeState(connection, TCPListen::Insctence());
  }
  
  ...

};

/**
* @brief ConcreteState - Established
* @details 해당하는 State의 인터페이스에 대해 행동을 구현
*/
class TCPListen : public TCPState {
  static TCPState* instence();

  void send(TCPConnection* connection) override {
    // 행동 정의

    ...

    // TCPConnection의 상태를 TCPListen으로 상태 변경
    changeState(connection, TCPEstablished::Insctence());  
  }
  
  ...

};

/**
* @brief ConcreteState - Established
* @details 해당하는 State의 인터페이스에 대해 행동을 구현
*/
class TCPClosed : public TCPState {
  static TCPState* instence();

  void activeOpen(TCPConnection* connection) override {
    // 행동 정의

    ...

    // TCPConnection의 상태를 TCPEstablished으로 상태 변경
    changeState(connection, TCPEstablished::Insctence());
  }

  void passiveOpen(TCPConnection* connection) override {
    // 행동 정의

    ...

    // TCPConnection의 상태를 TCPListen 상태 변경
    changeState(connection, TCPListen::Insctence());
  }

  ...

};


```

## Related Pattern

---

*State* 객체의 공유 시점과 공유 방법을 정의하는 데는 *Flyweight Pattern* 을 이용한다.

*State* 객체는 종종 하나만 존재할 때가 많은데, 이때는 **Singleton Pattern*을 이용한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
