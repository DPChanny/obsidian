---
type: language
parent: "[[C++]]"
related:
  - "[[Generals/OOP/Abstraction/Interface|Interface]]"
---
C++에서는 **순수 가상 함수(pure virtual function)**를 이용해 이 구조를 만든다. C++에는 `interface` 키워드가 없으므로, 이를 대신해 **모든 메서드가 순수 가상 함수인 추상 클래스(abstract class)**를 만든다.

---

## 기본 구조

```cpp
class IShape {
public:
    virtual double area() const = 0; // 순수 가상 함수
    virtual void draw() const = 0;
    virtual ~IShape() {} // 반드시 virtual 소멸자
};
```

이 클래스는 객체를 만들 수 없다. 대신, 이를 상속한 클래스는 반드시 `area()`와 `draw()`를 구현해야 한다. 이렇게 하면 "모양(shape)"이라는 공통 인터페이스를 가진 여러 구현체를 동일한 방식으로 다룰 수 있다.

---

## 예제: 다양한 도형 인터페이스

```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

class IShape {
public:
    virtual double area() const = 0;
    virtual void draw() const = 0;
    virtual ~IShape() {}
};

class Circle : public IShape {
private:
    double radius;
public:
    Circle(double r) : radius(r) {}
    double area() const override {
        return 3.14 * radius * radius;
    }
    void draw() const override {
        cout << "Drawing Circle, area = " << area() << endl;
    }
};

class Rectangle : public IShape {
private:
    double width, height;
public:
    Rectangle(double w, double h) : width(w), height(h) {}
    double area() const override {
        return width * height;
    }
    void draw() const override {
        cout << "Drawing Rectangle, area = " << area() << endl;
    }
};

int main() {
    vector<unique_ptr<IShape>> shapes;
    shapes.emplace_back(new Circle(3));
    shapes.emplace_back(new Rectangle(4, 5));

    for (const auto& shape : shapes) {
        shape->draw();  // 인터페이스를 통한 다형성 호출
    }
}
```

결과:

```
Drawing Circle, area = 28.26
Drawing Rectangle, area = 20
```

---

## 인터페이스의 특징과 제약

- 인터페이스는 **객체의 동작을 명세**하고, 실제 구현은 외부에 위임한다.
- 추상 클래스는 객체 생성이 불가능하다.
- `virtual` 소멸자는 필수. 다형성 파괴 방지.
- 일반적으로 인터페이스는 **상속용으로만 사용되기 때문에 멤버 변수는 두지 않는다.**