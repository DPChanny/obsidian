---
type: language
related:
  - "[[Generals/OOP/Polymorphism|Polymorphism]]"
---
## virtual을 이용한 다형성

```cpp
class B {
public:
    virtual void print() {
        std::cout << "B::print()\\n";
    }
};

class A : public B {
public:
    void print() override {
        std::cout << "A::print()\\n";
    }
};
```

```
ptr->print(); // A::print()
a.print();    // A::print()
```

virtual을 붙이면 런타임 시점에 실제 객체 기준으로 호출이 이뤄진다. 이게 바로 다형성이다. 이때 override는 선택적으로 붙인다.

또한 final을 override한 곳에 붙이면 더이상 override가 불가능하다.

```cpp
class Parent {
public:
    virtual void foo();
};

class Child : public Parent {
public:
    void foo() final; // 더 이상 오버라이딩 불가
};

// class GrandChild : public Child {
//     void foo(); // 오류: final 함수는 오버라이드 불가
// };
```

---

## virtual이 있어도 명시적으로 부모 함수 호출 가능

```cpp
obj.B::print(); // B::print()
```

virtual을 썼다고 해도 부모 함수가 사라지는 건 아니다. C++에서는 메모리 상에 부모 객체도 그대로 있으니 스코프 연산자로 직접 호출할 수 있다.

# 가상 상속 (Virtual Inheritance)과 다이아몬드 문제

---

## 문제 상황: 다이아몬드 상속 구조

다음과 같은 구조를 보자.

```
   Base
  /    \\
Left   Right
  \\    /
 Derived
```

- `Left`와 `Right` 모두 `Base`를 상속하고,
- `Derived`는 `Left`와 `Right`를 다시 상속

이때 `Base`에 있는 멤버가 **`Derived`에 두 번 들어오게 되는 문제**가 발생한다.

즉, **Base가 두 개 존재**하게 되는 것이다.

---

## 해결 방법: `virtual`을 붙인 가상 상속

이 문제를 해결하기 위해, `Left`와 `Right`가 `Base`를 **가상으로 상속**하면 된다.

```cpp
class Base {
public:
    int value;
};

class Left : public virtual Base {
};

class Right : public virtual Base {
};

class Derived : public Left, public Right {
public:
    void accessBase() {
        value = 42; // OK, 하나의 Base만 상속됨
    }
};
```

- `virtual` 키워드를 붙이면, **Left와 Right가 공유하는 단일 Base 인스턴스만 Derived에 포함된다.**
- 이 구조에서는 `Derived`가 `value`에 접근할 때 **모호성 없이 하나의 `Base`만 참조**하게 된다.

---

## 왜 `virtual`이 필요한가?

- `Left`와 `Right`가 **각자 Base를 별도로 상속하면**, `Derived` 안에는 `Base`가 **두 개 생긴다.**
- 이때 `Derived::value`를 쓰면 컴파일러가 “어느 `Base`의 `value`냐?”고 물어보게 된다.
- `virtual` 상속을 쓰면, **Base는 공유되고 하나로 합쳐진다.**

---

## 가상 함수 테이블 (vtable) 관련

- 추상 클래스는 **vtable(가상 함수 테이블) 포인터**를 내부에 갖는다.
- 순수 가상 함수 역시 **vtable 항목에 등록**되며, 자식 클래스에서 오버라이드되면 그걸로 교체된다.
- 만약 순수 가상 함수가 **오버라이드되지 않으면**, vtable에 null 엔트리로 남아있고, 호출 시 런타임 에러가 발생할 수 있다.

---

## 상속 시 구조

- 파생 클래스는 **기반 클래스의 vtable을 상속**한다.
- 순수 가상 함수를 오버라이드하면 **vtable에 자신의 함수 포인터로 채워진다.**
- 파생 클래스가 모든 순수 가상 함수를 구현하지 않으면, **자기 자신도 추상 클래스가 된다.**