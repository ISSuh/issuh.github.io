---
title: Design Patterns - Proxy
date: 2020-04-18 17:19:00
categories:
- Design Patterns
tags:
- Design Patterns
- Structural Patterns 
- Proxy
- C++
---

# Structural Patterns - Proxy

## Intent

---

다른 객체에 대한 접근을 제어하기 위한 대리자 또는 자리채움자 역할을 하는 객체를 둔다.

대리자(Surrogate)라고도 불린다.

## Utility

---

*Proxy Pattern*은 단순한 포인터보다는 조금 더 다방면에 활용 가능하다. 정교한 객체 참조자가 필요한 때 적용할 수 있다.

- **원격지 프록시(Remote Proxy)**
  - 서로 다른 주소 공간에 존재하는 객체를 가리키는 대표 객체로, 로컬환경에 위치한다.
- **가상 프록시(Virtual Proxy)**
  - 요청이 있을 때만 필요한 고비용 객체를 생성한다.
- **보호용 프록시(Protection Proxy)**
  - 원래 객체레 대한 실제 접근을 제어한다.
  - 객체별로 접근 제어 권한이 다를 때 유용하게 사용 가능하다.
- **스마트 참조자(Smart Reference)**
  - 원시 포인터의 대체용 객체로, 실제 객체에 접근이 일어날때 추가적인 행동을 수행한다.
  - 전형적인 사용예시
    - 실제 객체에 대한 참조 횟수를 저장하다가 더는 참조가 없을 때 해당 객체를 자동으로 없앤다.(스마트포인터)
    - 맨 처음 참조되는 시점에 영속적 저장소의 객체를 메모리로 옮긴다.
    - 실제 객체에 접근하기 전에, 다른 객체가 그것을 변경하지 못하도록 실제 객체에 대해 잠금(lock)을 건다.

## Structure

---

### Basic Structure

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/proxy017.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/proxy015.gif)

### Structure Example

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/proxy016.gif)

![example](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/proxy-eg.gif)

### Elements

- **Proxy**
  - *ImageProxy*에 해당
  - 실제로 참조할 대상에 대한 참조자를 관리
    - *RealSubject*와 *Subjet* 인터페이스가 동일하면 *Proxy*는 *Subject*에 대한 참조자를 가짐
  - *Subject*와 동일한 인터페이스를 제공하여 실제 대상을 대체할 수 있어야 함
  - 실제 대상에 대한 접근을 제어하고, 실제 대상의 생성과 삭제에대한 챔임을 짐
  - *Proxy*의 종류에 따라 다음을 수행
    - *Remote Proxy* : 요청 메시지와 인자를 인코딩하여 이를 다른 주소공간에 있는 실제 대상에게 전달
    - *Virtual Proxy* : 실제 대상에 대한 추가적 정보를 보유하여 실제 접근을 지연할 수 있도록 해야 함
    - *Protection Proxy* : 요청한 대상이 실제 요청할 수 있는 권한인지 확인
- **Subject**
  - *Graphic*에 해당
  - *RealSubject*와 *Proxy*에 공통적인 인터페이스를 정의하여 *RealSubject*가 요청되는 곳에 *Proxy*를 사용할 수 있게 함
- **RealSubject**
  - *Image*에 해당
  - *Proxy*가 대표하는 실제 객체

*Proxy* 클래스는 자신이 받은 요청을 *RealSubject* 객체에 전달한다.

## Feature

---

*Proxy Pattern*은 어떤 객체에 접근할 떄 추가적인 간접화 통로를 제공한다.

- *Remote Proxy*는 객체가 다른 주소 공간에 존재한다는 사실을 숨길수 있게 해준다.
- *Virtual Proxy*는 요구에 따라 객체를 생성하는 등의 처리를 최적화할 수 있게 해준다.
- *Protection Proxy*및 *Smart Reference*는 객체의 생성과 삭제를 관리하며, 객체가 접근할 때마다 추가 관리를 책임진다.

*Proxy Pattern*이 사용자에게 숨길수 있는 또 다른 최적화로는, **기록 시점 복사(Copy on Write)** 이다. 이 최적화는 요구가 들어올 때만 객체를 생상하는 개념과 관련있다. 덩치가 크고 복잡한 객체를 복사하려면 비용이 매우 커지게 된다. 만약 사본이 변경되지 않고 원본과 똑같다면, 이러한 비용을 지불할 필요가 없게된다. 이때 *Proxy*를 사용해서 복사 절차를 미룸으로써, 사본이 수정될 때만 실제 복사 비용을 지불하게 한다.

이 *기록 시점 복사* 기능 가능하게 하려면 원본의 참조 카운트를 관리해야 한다. *Proxy*를 복사하는 연산은 이 원본에 대한 참조 카운트를 증가시키는 일 외에 다른일은 하지 않는다. 사용자가 이 원본을 수정하는 연산을 요청할 때 *Proxy*가 실제로 복사를 진행하여 사본이 별도의 값을 가지게 한다. 이렇게 되면 원본에 대한 참조자 수는 줄어들게 된다. 이런 과정의 반복속에 참조 카운트가 0이 되면 대상을 삭제한다.

## Implementation

---

### Considerations point in implementaion

#### C++에서는 멤버 접근 연산자를 오버로드 하여 구현

C++에서의 멤버 접근 연사자인 *operator->* 연산을 오버로드하여, 포인터를 통해 해당 객체에 접근할 떄마다 뭔가 추가적인 행동을 할 수 있도록 한다. *Proxy*가 단순한 포인터의 역할만 수행할 때는 매우 유용한 방식이다.

```cpp
// RealSubject
class Image;

// 외부함수
extern Image* LoadAnImageFile(const char*);

// ImagePtr Virtual Proxy
class ImagePrt {
public:
  ImagePrt(const char* imageFile){
    m_imageFile = imageFile;
    m_image = 0;
  }

  virtual ~ImagePtr();


  // LoadImage() 연산을 이용하여 m_image를 호출자에게 반환
  virtual Image* operator->() { return LoadImage(); }

  // LoadImage() 연산을 이용하여 m_image를 호출자에게 반환
  virtual Image& operator*() { return LoadImage(); }

private:
  Image* LoadImage() {
    if(m_image == 0)
      m_image = LoadAnImageFile(m_imageFile);

    return m_image;
  }

private:
  Image* m_image;
  const char* m_imageFile;
};

...

  ImagePtr iamge = ImagePtr("anImageFilename");
  
  // (image.operator->())->Draw(Point(50, 100))
  image->Draw(Point(50, 100));

...

```

멤버 함수로 정의된 접근 연산자를 오버로드하는 것은 모든 종류의 *Proxy* 구현에 적합한 방법은 아니다. 어떤 *Proxy*는 어떤 연산이 호출되는지 알아야 사용할 멤버 접근 연산자를 정할 수 있다.

이 연산들은 대체로 서로 비슷하게 구현된다. 모든 연산에서는 실제 요청 처리를 담당할 대상에게 전달하기 전에 이 요청이 합당한지 원본 객체가 존재하는지 등 검증한다. 모든 연산마다 이러한 코드를 작성하기에는 비효율적이므로, 일반적으로 이런 반복코드를 자동으로 생성하는 선행 처리자를 두어 처리한다.

#### *Proxy*가 항상 자신이 상대할 실제 대상을 알 필요는 없음

*Proxy* 클래스는 추상 인터페이스를 통해서만 대상과 일을 하므로, *RealSubject*별로 *Proxy* 클래스를 만들 필요가 없다. *Proxy*는 모든 *RealSubject*를 마치 하나의 클래스인 것처럼 하나의 인터페이스를 통해 사용하면 된다. 그러나 *Proxy*는 *RealSubject*의 인스턴스를 생성해야 하는 시점이 되면 어떤 클래스의 인스턴스를 생성해야 하는지 알아야 한다.

#### 대상 인스턴스를 만들기 전에, 그 대상을 어떨게 알 것인가

대상이 메모리 또는 디스크에 있던 대상을 항상 참조해야 하는 *Proxy*가 있을수 있다. 이 말은 객체의 저장 공간과 관계없는 객체 식별자를 사용해야 한다는 말이다. 이때는 파일 이름과 같은 식별자를 사용하여 대상을 추측한다.

### Implement Example

```cpp

// Clone Minecraft's block(cube)

/**
* @brief Subject - Graphic
* @details 공통 인터페이스를 정의하여 실제 객체가 요청되는곳에 Proxy를 사용할 수 있게 함
*/
class Graphic {
public:
  virtual ~Graphic() {}

  virtual void draw(const Point& at) = 0;
  virtual void handleMouse(Event& event) = 0;

  virtual const Point& getExtent() = 0;

  virtual void load(istream& from) = 0;
  virtual void save(ostream& to) = 0;

private:
  Graphic();
};

/**
* @brief RealSubject - Image
* @details Proxy가 대표하는 실제 객체
*/
class Image : public Graphic {
public:
  // 파일로부터 이미지를 로드
  Image(const cahr* file) {}
  virtual ~Image() {}

  virtual void draw(const Point& at);
  virtual void handleMouse(Event& event);

  virtual const Point& getExtent();

  virtual void load(istream& from);
  virtual void save(ostream& to);

private:
  
  ...

};

/**
* @brief Proxy - ImageProxy
* @details 실제 객체에 접근하기 위한 Proxy
*/
class ImageProxy : public Image {
public:

  ImageProxy(const cahr* fileName) {
    m_fileNmae = strdup(fileName);
    m_extent = Point::Zero;
    m_image = 0;
  }

  virtual ~ImageProxy() {}


  virtual void draw(const Point& at) { getImage()->Draw(at) }
  virtual void handleMouse(Event& event) {getImage()->handleMouse(event); }


  virtual const Point& getExtent() {
    if(m_extent == Point::Zero)
      m_extent = getImage()->getExtent();

    return m_extent;
  }


  virtual void load(istream& from) { to << m_extent << m_fileNmae; }
  virtual void save(ostream& to) { from >> m_extent >> m_fileNmae; }

protected:
  Image* getImage() {
    if(m_image == 0)
      m_image = new Image(m_fileName);

    return m_image;
  }

private:
  Image* m_image;
  Point m_extent;
  char* m_fileName;
};

/**
* @brief Client - TextDocument
* @details RealSubject를 요청하여 Proxy에 접근
*/
class TextDocument {
public:
  TextDocument();

  void insert(Graphic* );

  ...

};


...

  TextDocument* text -> new TextDocument();

  ...

  text->insert(new ImageProxy("anImageFileName"));

...

```

## Related Pattern

---

*Adapter*는 자신이 개조할 객체가 정의된 인터페이스와 다른 인터페이스를 제공한다. 이에 반해 *Proxy*는 자신이 상대하는 대상과 동일한 인터페이스를 제공한다. *Proxy*는 대상이 수행할 연산의 수행을 거부할 수도 있기 때문에, 처리 대상이 제공하는 인터페이스의 부분 집합 일수도 있다.

*Decorator*는 *Proxy*와 구현 방법이 비슷한데, *Decorator*는 그 사용 목적이 하나 이상의 서비스를 추가하기 위한 것이고, *Proxy*는 객체에 대한 접근을 제어하는 목적이라는 점에서 차이가 있다.

*Proxy*는 *Decorator*와 구현 밥법에도 많은 차이를 보인다. 예를 들어 *Protection Proxy*는 *Decorator* 구현 방법이 거의 유사하나, *Remote Proxy*는 실제 처리 대상을 직접 참조하도록 관리하지 않고 간접적 접근방법을 관리한다. 이 간접적 접근 방법은 호스트 식별자, 호스트 머신 내의 주소 등을 포함한다. *Virtual Proxy*는 파일 이름과 같은 간접적 참조자를 정의하지만, 궁극적으로는 직접적 참조자를 얻어온 후 이를 사용한다.

---
---
*참고*. Design Patterns : Elements of Reusable Object-Oriented Software - Erich Gamma

---
