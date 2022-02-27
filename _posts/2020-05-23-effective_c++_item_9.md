---
title: Effective C++ - Item.9
date: 2020-05-23 12:14:00
categories: [C++, Effective C++]
tags:
- C++
- Effective C++
---

# 객체 생성 및 소멸 과정 중에는 절대로 가상 함수를 호출하지 말자

---

## 내용

### 파생 클래스 생성시 주의점

**파생 클래스 객체의 기본 클래스 부분이 생성되는 동안에는, 그 객체의 타입은 바로 기본 클래스이다.** 호출되는 가상 함수는 모두 기본 클래스의 것으로 결정(resolve)될 뿐만 아니라, 런타임 타입 정보를 사용하는 언어요소(*dynamic_cast*, *typeid* 등)을 사용한다고 해도 이 순간엔 모두 기본 클래스 타입의 객체로 취급한다.

### 객체 생성 과정중에서 가상함수 호출

객체 생성 및 소멸 과정에서 가상함수를 호출하면 안된다. 크게 두가지 이유가 있는데, 우선 호출 결과가 원하는대로 돌아가지 않을 것이고, 원하는대로 돌아 간다고 해도 잠재적인 위험를 가지고 있다.

주식 거래에 대한 클래스 계통 구조를 구현한다면 아래와 같이 할 수 있다.

```cpp
//모든 거래에 대한 기본 클래스
class Transaction {
 public:
  Transaction() {
    ...
    logTransaction();   // 동작에 대한 로그 기록
  }

  // 타입에 따라 달라지는 로그 기록
  virtual void logTransaction() const = 0;
  ...
};

// Transaction의 파생 클래스
class BuyTransaction : public Transaction {
 public:
  // 이 타입에 대한 거래내역의 로깅 구현
  virtual void logTransaction() const {
    ...
  }
};

// Transaction의 파생 클래스
class SellTransaction : public Transaction {
 public:
  // 이 타입에 대한 거래내역의 로깅 구현
  virtual void logTransaction() const {
    ...
  }
};


...

  BuyTransaction b;

...
```

*BuyTransaction* 생성자가 호출되기 전에 *Transaction* 생성자가 먼저 호출된다. 파생 클래스 객체가 생성될 때 그 객체의 기본 클래스 부분이 파생 클래스 부분보다 먼저 호출되는것이 정석이기 때문이다. *Transaction* 클래스의 생성자 마지막에 *logTransaction()* 를 호출하여 로깅을 하게되는데, 이때 호출되는 *logTransaction()* 는 파생클래스인 *BuyTransaction*의 *logTransaction()* 가 아닌 기본 클래스인 *Transaction*의 *logTransaction()* 이다. 이와같이 **기본 클래스의 생성자가 호출될 동안에는, 가상 함수는 절대로 파생 클래스 쪽으로 내려가지 않는다**. 그 대신, 객체 자신이 기본 클래스 타입인 것처럼 동작한다. 즉, 기본 클래스 생성 과정에서는 가상 함수가 동작하지 않는다.

위와 같이 동작하는 이유는, 기본 클래스 생성자는 파생 클래스 생성자보다 앞서서 실행되기 때문에, 기본 클래스 생성자가 돌아가고 있는 시점에 파생 클래스 데이터 멤버는 아직 초기화된 상태가 아니라는 것이 핵심이다. 이때 기본 클래스 생성자에서 어떠다 호출된 가상 함수가 파생클래스 쪽으로 내려간다면 어떻게 될 것인가? 파생 클래스 버전의 가상 함수는 파생 클래스만의 데이터 멤버에 접근하여 사용할 것인데, 이때 이 데이터 멤버들은 초기화되기 전 일 것이다. 즉, 파생 클래스의 가상 함수는 초기화되지 않은 데이터 멤버에 접근하여 사용할 것이고, 이는 치명적인 위험을 내포하고 있다.

### 객체 소멸 과정중에서 가상함수 호출

객체가 소멸될(소멸자가 호출될) 때도 생성될 때와 똑같이 생각하면 된다. 파생 클래스의 소멸자가 일단 호출되고 나면 파생 클래스만의 데이터 멤버는 정의되지 않은 값으로 가정하기 때문에, 이제부터 이들을 없는 것처럼 취급하고 진행한다. 기본 클래스 소멸자에 진입할 당시의 객체는 기본 클래스 타입의 객체가 되며, 모든 C++ 기능(*가상 함수*, *dynamic_cast*, 등) 역시 기본 클래스 객체의 자격으로 처리한다.

### 대처 방법

위와 같이 객체 생성, 소멸과정중에 가상함수를 호출하는 문제에 대한 대처 방법은 여러가지가 있는데, 그중 한가지 방법은 가상 함수를 비가상 함수로 바꾸는 것이다.

```cpp
class Transaction {
 public:
  explicit Transaction(const std::string& log) {
    ...

    // 비가상 함수를 호출
    logTransaction(log);
  }

  // 비가상 함수로 변경
  void logTransaction(const std::string& log) const;
  ...
};

class BuyTransaction : public Transaction {
 public:
  // 로그 정보를 기본 클래스 생성자로 전달
  BuyTransaction( parameters ) : Transaction(createLog( parameters )) {
    ...
  }
  ...

 private:
  static std::string createLog( parameters );
};
```

기본 클래스 부분이 생성될 떄는 가상 함수를 호출한다 해도 기본 클래스의 울타리를 넘어 내려갈 수 없기 때문에, 필요한 초기화 정보를 파생 클래스 쪽에서 기본 생성자로 '*올려*'주도록 하여 부족한 부분을 역으로 채울 수 있게 된다.

*BuyTransaction* 클래스애서 선언된 *createLog()* 이라는 정적 함수가 사용되고 있는 부분에 대하여, 이 함수는 기본 클래스 생성자 쪽으로 넘길 값을 생성하는 용도로 쓰이는 도우미 함수이다. 이 함수는 기본 클래스에 멤버 초기화 리스트가 여러가지로 달려 있는 경우에 특히 유용하게 쓰일수 있다. 정적 멤버로 되어 있기 때문에, 생성이 채 끝나지 않은 *BuyTransaction* 객체의 초기화 되지 않은 데이터 멤버를 자칫 실수로 접근할 위엄도 없다. 이는 매우 중요한데, **'초기화 되지 않은 데이터 멤버는 정의되지 않은 상태에 있다'** 라는 사실 때문이다. 비로 이것 때문에 기본 클래스 부분의 생성과 소멸이 진행되고 있는 동안에 호출되는 가상 함수가 무턱대고 파생 클래스 쪽으로 내려가지 않는 것이다.

## 요점

> 가상 함수라고 해도, 실행중인 생성자나 소멸자에 해당되는 클래스의 파생 클래스 쪼으로 내려가지 않기 때문에 생성자 혹은 소멸자 안에서 가상함수를 호출하지 않는다.

---
---
*참고*. Effective C++ 3/E - Scott Meyers

---
