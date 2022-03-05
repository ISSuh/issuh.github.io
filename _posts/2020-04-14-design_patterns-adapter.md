---
title: Design Patterns - Adapter
date: 2020-04-14 21:08:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns
- Adapter
- C++
---

# Structural Patterns - Adapter

## Intent

---

클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 정응(변환)시킨다. 서로 일치하지 않는 인터페이스를 가지는 클래스들을 함께 동작시킨다.

## Utility

---

- 기존 클래스를 사용하고 싶은데 인터페이스가 맞지 않을 때
- 이미 만든 것을 재사용하고자 하나 이 재사용 가능한 라이브러리를 수정할 수 없을 때
- *[객체 적응자(Object Adapter)만 해당됨]* 이미 존재하는 여러개의 서브클래스를 사용해야 하는데, 이 서브클래스들의 상속을 통해서 이들의 인터페이스를 다 수정하는 것에 비용이 많이들 때

## Structure

---

### Basic Structure

#### 클래스 적응자(Class Adapter) 활용

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/adapt106.gif)

#### 객체 적응자(Object Adapter) 활용

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/adapt104.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/adapt105.gif)

### Elements

- **Target**
  - *Shape*에 해당
  - 사용자가 사용할 응용 분야에 종속적인 인터페이스를 정의하는 클래스
- **Client**
  - *DrawingEditor*에 해당
  - *Target* 인터페이스를 만족하는 객체와 동작할 대상
- **Adaptee**
  - *TextView*에 해당
  - 인터페이스의 적응이 필요한 기존 인터페이스를 정의하는 클래스
- **Adapter**
  - *TextShape*에 해당
  - *Target* 인터페이스에 *Adaptee*의 인터페이스를 적응시키는 클래스 

사용자는 *Adapter*에 해당하는 클래스의 인스턴스에게 연산을 호출하고, *Adapter*는 해당 요청을 수행하기 위해 적응대상자의 연산을 호출한다.

## Feature

---

### 대체가능(Pluggable) 적응자

다른 인터페이스를 원하는 사용자가 *Adapter* 클래스를 통해 연결하거나, 인터페이스의 개조를 통해 기존에 존재하는 시스템과 함께 연동할 수 있도록 하는 것을 *대체가능 적응자(Pluggable Adapter)* 라고 한다.

### 양방향 *Adapter*를 통한 투명성 제공

*Adapter*의 잠재적인 문제는 *Adapter*가 모든 사용자에게 투명하지 않다는 것이다. 적응된 객체는 더는 *Adaptee* 인터페이스를 만족하지 않는다. *Target*의 인터페이스를 만족하기 때문이다. 이렇게 되면 *Target*이 필요한 사용자는 적응된 클래스를 사용할 수 있지만, *Adaptee* 객체를 통해 *Target*을 사용해야 하는 사용자라면 적응된 객체를 사용할 수 없게 된다. 양방향 적을이 되려면 이런 상황에서 모두 지원해야 한다.

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/adapt107.gif)

### 클래스 적응자(Class Apdater)와 객체 적응자(Object Apdater)

#### 클래스 적응자(Class Apdater)

*Adapter*는 명시적으로 *Adaptee*를 상속박고 있을뿐, *Adaptee*의 서브클래스들을 상속받는 것이 아니므로, *Adaptee*의 서브클래스에 정의된 기능들을 사용할 수 없다.

*Adapter* 클래스는 *Adaptee* 쿨래스를 상속하기 때문에 *Adaptee*에 정의된 행동을 재정의를 할 수 있다.

헌개의 객체(*Adapter*)만 사용하며, *Adaptee*로 가기 위한 추가적인 포인터 간접화는 필요로하지 않는다.

#### 객체 적응자(Object Apdater)

*Adapter* 객체가 포함하는 *Adaptee*에 대한 참조자는 *Adaptee*의 인스턴스를 관리할 수도 있고, *Adaptee* 클래스를 상속받는 다른 서브클래스들의 인스턴스도 관리할 수 있기 때문에, 하나의 *Adapter* 클래스로 모든 *Adaptee* 클래스와 이를 상속받는 서브클래스 모두를 이용가능하다.

*Adaptee* 클래스의 행동을 재정의하기가 매우 어렵다.

## Implementation

---

### Implementation Method

#### 클래스 적응자 구현

C++에서 *Adapter* 클래스는 *Target* 클래스에서 public으로 상속받고, *Adaptee*는 private로 상속받아야 한다. 즉, *Target*에 정의된 인터페이스는 *Adapter*에서도 public으로 공개되지만, *Adaptee*는 내부 구현에 필요한 것이므로, *Adaptee*가 사용자에게 알려질 필요가 없다.

위와같이 구현하게 되면 *Adapter*는 *Target*의 서브클래스이기는 하지만, *Adaptee*의 서브클래스는 아니게 된다.

### Implement Example

#### 클래스 적응자(Class Adapter) Example 

```cpp
/**
* @brief Taget - Shape
* @details 재정의할 interface를 가진 class
*/
class Shape {
public:
  Shape();
  virtual void boundingBox(Point& bottomleft, Point& topRight) const;
  virtual Manipulator* createManipulator() const;
};

/**
* @brief Adaptee - TextView
* @details 사용할 함수를 가진 class
*/
class TextView {
public:
  TextView();
  void getOrigin(Coord& x, Coord& y) const;
  void getExten(Coord& width, Coord& height) const;
  virtual bool isEmpty() const;
};

/**
* @brief Adapter - TextShape
*/
class TextShape : public Shape, private TextView {
public:
  TextShape();

  // Shape 클래스의 인터페이스를
  // TextView 클래스의 멤버함수를 이용하여 기능 정의
  void boundingBox(Point& bottomleft, Point& topRight) const override {
    Coord bottom, left, width, height;
    
    getOrigin(bottom, left);
    getExten(width, height);

    bottomleft = Point(bottom, left);
    topRight = Point(bottom + height, left + width);
  }

  // Adapter 구현에 공통적인 요청을 직접 전달 
  bool isEmpty() const override { return TextView::IsEmpty(); }

  // 완전히 새로운 구현을 통해 새로운 서비스의 추가
  Manipulator* createManipulator() const override { return new TextManipulator(this); }
};

```

#### 객체 적응자(Object Adapter) Example 

```cpp
/**
* @brief Taget - Shape
* @details 재정의할 interface를 가진 class
*/
class Shape {
public:
  Shape();
  virtual void boundingBox(Point& bottomleft, Point& topRight) const;
  virtual Manipulator* createManipulator() const;
};

/**
* @brief Adaptee - TextView
* @details 사용할 함수를 가진 class
*/
class TextView {
public:
  TextView()

  void getOrigin(Coord& x, Coord& y) const;
  void getExten(Coord& width, Coord& height) const;
  virtual bool isEmpty() const;
};

/**
* @brief Adapter - TextShape
*/
class TextShape : public Shape {
public:
  // 다른 클래스의 객체에 대한 포인터를 관리하기 위해,
  // TextView 인스턴스에 대한 포인터를 초기화 할수 있는 생성자 구현
  TextShape(TextView* t) { m_textView = t; }

  // 객체를 통해 정의된 연산을 호출
  void boundingBox(Point& bottomleft, Point& topRight) const override {
    Coord bottom, left, width, height;
    
    // 객체 참조자를 통한 함수 호출
    m_textView->getOrigin(bottom, left);
    m_textView->getExten(width, height);

    bottomleft = Point(bottom, left);
    topRight = Point(bottom + height, left + width);
  }

  // 객체를 통해 정의된 연산을 호출
  bool isEmpty() const { return m_textView->isEmpty(); }

  // 새롭게 구현하는 부분이기 때문에 클래스 적응자와 차이 없음
  Manipulator* createManipulator() const override { return new TextManipulator(this); }

private:
  // TextView에 대한 참조자
  TexView* m_textView = nullptr;
};

```

## Related Pattern

---

*Bridge Pattern*은 *object Adapter*와 클래스 구조가 유사하나 그 사용 목적이 다르다.  
*Bridge Pattern*은 구현과 이 구현이 만족할 추상 개념을 분리하여 서로에게 영향을 주지 않고 각각 확장할 수 있도록 하려는 것이고, *Adapter Pattern*은 존재하는 객체의 인터페이스를 변경하려는 것이다.

*Decorator Pattern*은 다른 인터페이스의 변결 없이도 객체에 새로운 행동을 추가할 수 있도록 한다.

*Proxy Pattern* 다른 객체에 대한 대표자 또는 대리인의 역할을 수행하지만 인터페이스를 변경하는 책임은 없다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
