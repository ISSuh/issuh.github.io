---
title: Design Patterns - Flyweight
date: 2020-04-18 15:22:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns 
- Flyweight
- C++
---

# Structural Patterns - Flyweight

## Intent

---

공유(sharing)을 통해 많은 수의 소립(fine-grained) 객체들을 효과적으로 지원한다.

## Utility

---

*Flyweight Pattern*은 언제 사용하는가에 따라서 그 효과가 달라지게 된다.

- 응용프로그램이 대량의 객체를 사용해야 할 때
- 객체의 수가 너무 많아져 저장 비용이 너무 높아질 때
- 대부분의 객체 상태를 부가적인 것으로 만들수 있을 때
- 부가적인 속성들을 제거한 후 객체들을 조사해 보니 객체의 많은 묶음이 비교적 적은 수의 공유된 객체로 대체될 수 있을 떄
- 현재 서로 다른 객체로 간주한 이유는 이들 부가적인 속성 때문이지 본질이 달랐던 것은 아닐 때
- 응용프로그램이 객체의 정체성에 의존하지 않을 때
  - *Flyweight* 객체들은 공유 될 수 있음을 의미하는데, 식별자가 있다는 것은 서로 다른 객체로 구별해야 한다는 의미 이므로 *Flyweight* 객체를 사용할 수 없다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/flywe050.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/flywe051.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/flywe055.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/flywe054.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/flywe052.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/flywe053.gif)

### Elements

- **Flyweight**
  - *Graph*에 해당
  - *Flyweight*가 받아들일 수 있고, 부가적인 상태에서 동작해야 하는 인터페이스를 정의
- **ConcreteFlyweight**
  - *Character*에 해당
  - *Flyweight* 인터페이스를 구현
  - 내부적으로 가지고 있어야 하는 본질적 상태에 대한 저상소를 정의
  - *ConcreteFlyweight* 객체는 공유할 수 있어야 하므로, 관리하는 어떤 상태라도 본질적 이여야 함
- **UnsharedConcreteFlyweight**
  - *Row*, *Column*에 해당
  - *Flyweight* 인터페이스는 공유를 가능하게 하지만, 그것을 강요해서는 안됨. 즉, 모든 *Flyweight*의 서브클래스들이 공유될 필요는 없음
  - *UnsharedConcreteFlyweight* 객체는 *ConcreteFlyweight* 객체는 자신의 자식으로 가질수도 있음
- **FlyweightFactory**
  - *Flyweight* 객체를 생성하고 관리하며, *Flyweight* 객체가 제대로 공유되도록 보장
  - 사용자가 *Flyweight* 객체를 요청하면, *FlyweightFactory* 객체는 이미 존재하고 있는 인스턴스를 제공하거나 만약 존재하지 않으면 새로 생성
- **Client**
  - *Flyweight* 객체레 대한 참조자를 관리하며 객체의 부가적 상태를 저장

*Flyweight* 객체가 기능을 수행하는데 필요한 상태가 본직적인지, 부가적인 것인지를 구분해야 한다. 본질적인 상태는 *ConcreteFlyweight*에 저장해야 하고, 부가적인 상태는 사용자가 저장하거나, 연산되어야 하는 다른 상태로 관리해야 한다.

사용자는 *ConcreteFlyweight*의 인스턴스를 직접 만들수 없다. 사용자는 *ConcreteFlyweight* 객체를  *FlyweightFactory* 객체에서 얻어야 한다. 이렇게 해야 *Flyweight* 객체가 공유될 수 있다.

## Feature

---

*Flyweight Pattern*은 예전에는 모두 본질적인 상태로 저장되어 있던 것을 부가적인 상태로 만들어, 부가적인 상태의 연산과 전송에 드는 런타임 비용을 새로 들여올 수 있다. 하지만 이런 비용은 *Flyweight* 객체의 공유를 통해 저장소 절약이라는 반대급부를 가질 수 있다.

- 공유해야 하는 인스턴스의 전체 수를 줄일 수 있다.
- 객체별 본질적 상태의 양을 줄일 수 있다.
- 부가적인 상태는 연산되거나 저장될 수 있다.

더 많은 *Flyweight*가 공유될 수록 저장소는 절약된다. 또한 공유할 상태가 많아 질수록 절약된다. 대부분의 본질적인 상태가 저장되고 부가적인 상태는 연산될 때라면 절약의 효과가 가장 크다. 이때는 본질적인 상태를 저장하는 비용이 줄어드는 대신, 부가적인 상태를 만들기 위한 연산의 시간이 들게 된다.

*Flyweight Pattern*은 *Composite Pattern*과 조합하여 그래프와 같이 계층적 구조를 모델링하는데 자주 사용된다. 이렇게 하면, *Flyweight* 단말 노드들은 자신의 부모에 대한 포인터를 저정할 수 없기 때문에, 부모 포인터를 부가적 상태의 일부로서 *Flyweight* 객체에다가 매개변수로 전달해야 한다.

## Implementation

---

### Considerations point in implementaion

#### 부가적 상태를 제외

*Flyweight Pattern*의 활용여부는 얼마나 쉽게 공유할 객체에서 부가적인 상태를 식별하고 이를 분리하는데 달려있다.

#### 공유할 객첼를 관리

객체는 공유할 수 있으므로, 사용자가 직접 인스턴스를 만들면 안된다. 그렇기 때문에 *FlyweightFactory*는 사용자가 특정한 *Flyweight* 객체를 찾아내게 한다.*FlyweightFactory* 객체는 연관 저장소를 써서 사용자가 자신이 관심있는 *Flyweight* 객체를 찾아볼 수 있게 한다.

### Implement Example

```cpp

// Clone Minecraft's block(cube)

/**
* @brief Flyweight - CubeInfo
* @details 다량으로 생성될 객체들의 집합에서 공유될 공통 인터페이스 정의
*/
class Cube{
public:
  virtual ~Cube() {}

  virtual CubeType getCubeType() const = 0;

  virtual void setColor(ColorType colorType) = 0;
  virtual ColorType getColor() const  = 0;

  virtual ItemType getItem() const = 0;
}

/**
* @brief ConcreteFlyweight - RockCube
* @details Flyweight을 상속받아 공유되는 자신만의 인터페이스를 구현
*/
class RockCube : public Cube {
public:
  RockCube() { m_type = CUBE_TYPE_ROCK; }
  virtual ~RockCube() {}

  CubeType getCubeType() const override { return m_type; }

  void setColor(ColorType colorType) { m_color = m_color; }
  ColorType getColor() const override { return m_color; }

  ItemType getItem() const { return ITEM_TYPE_CUBE_ROCK; };

private:
  ColorType m_color;
  CubeType m_type;
};

/**
* @brief ConcreteFlyweight - SandCube
* @details Flyweight을 상속받아 공유되는 자신만의 인터페이스를 구현
*/
class SandCube : public Cube {
public:
  SandCube() { m_type = CUBE_TYPE_SAND; }
  virtual ~SandCube() {}

  CubeType getCubeType() const override { return m_type; }

  void setColor(ColorType colorType) { m_color = m_color; }
  ColorType getColor() const override { return m_color; }

  ItemType getItem() const { return ITEM_TYPE_CUBE_SAND; };

private:
  ColorType m_color;
  CubeType m_type;
};

/**
* @brief UnsharedConcreteFlyweight - MasterCube
* @details Flyweight을 상속받아 자신만의 인터페이스를 구현하지만 공유되지는 않음`
*/
class MasterCube : public Cube {
public:
  MasterCube() { m_type = CUBE_TYPE_MASTER; }
  virtual ~MasterCube() {}

  CubeType getCubeType() const override { return m_type; }

  void setColor(ColorType colorType) { m_color = m_color; }
  ColorType getColor() const override { return m_color; }

  ItemType getItem() const { return ITEM_TYPE_NONE; };

  void getPosition(Position pos) { m_pos = pos; }
  const Position getPosition() const { return m_pos; }

private:
  Position m_pos;
  ColorType m_color;
  CubeType m_type;
};

/**
* @brief FlyweightFactory - CubeFacotory
* @details ConcreteFlyweight 객체의 공유 및 생성을 위한 메소드 정의
*/
class CubeFacotory {
public:
  
  // 공유되는 성질을 가지는 ConcreteFlyweight 객체 생성 및 반환
  Cube* getCubeInstance(CubeType cubeType){

    // 현재 FlyweightFactory에 검색하는 타입의 ConcreteFlyweight 객체가 없을시,
    // 해당하는 타입의 ConcreteFlyweight 객체 생성
    if(m_cubeNap.find(cubeType) == m_cubeMap.end()){
      switch(cubeType){
        case CUBE_TYPE_ROCK : {
          m_cubeMap[cubeType] = dynamic_cast<Cube*>(new RockCube());
          break;
        }
        case CUBE_TYPE_SAND : {
          m_cubeMap[cubeType] = dynamic_cast<Cube*>(new SandCube());
          break;
        }
      }
    }

    return m_cubeMap[cubeType];
  }

  // 공유되지 않은 성질을 가지는 UnsharedConcreteFlyweight 객체 생성
  Cube* CreateMasterCube(){ new MasterCube(); }

private:
  std::map<CubeType, Cube*> m_cubeMap;
};

...

  CubeFacotory cubFactory;
  std::vector<Cube*> myCubeList;
  
  for(auto i = 0; i < 1000 ; ++i){
    CubeType cubeType = randomCubeType();
    myCubeList.push_back(cubFactory.getCubeInstance(cubeType));
  }

  myCubeList.push_back(cubFactory.CreateMasterCube());

...

```

## Related Pattern

---

*Flyweight*은 *Composite Pattern* 패턴과 함께 사용되는데, 공유되는 단말 노드를 가지는 방향성 비순환 그래프 형태를 써서 논리적으로 계층구조는 구현하는 것이 이에 해당한다.

*State Pattern*또는 *Strategy Pattern*을 *Flyweight* 객체로 구현할 수 있다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
