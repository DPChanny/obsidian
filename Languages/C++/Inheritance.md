---
type: language
parent: "[[C++]]"
related:
  - "[[Generals/OOP/Inheritance|Inheritance]]"
---
## 메모리 상속 구조 예시

```cpp
class B {
public:
    int b1;
    int b2;

    B() : b1(1), b2(2) {}
};

class A : public B {
public:
    int a1;
    int a2;

    A() : a1(3), a2(4) {}
};
```

이런 구조에서 실제 메모리 배치를 확인해 보면 다음과 같이 나온다:

```
Memory addresses:
 &obj.b1 = 0x7ffeecc2ba10
 &obj.b2 = 0x7ffeecc2ba14
 &obj.a1 = 0x7ffeecc2ba18
 &obj.a2 = 0x7ffeecc2ba1c

Offset check (bytes):
 a1 - b1 = 8
 a2 - a1 = 4

Object size:
 sizeof(A) = 16
```

C++은 상속한 객체를 명확하게 따로 배치한다. 그래서 shadowing이 발생하더라도 Scope Operator(`::`)를 이용해 우회가 가능하다.

---

## 멤버 변수 shadowing과 접근 우회

```cpp
class B {
public:
    int value = 10;
};

class A : public B {
public:
    int value = 20;

    void print() {
        std::cout << "A::value = " << value << "\\n";
        std::cout << "B::value = " << B::value << "\\n";
    }
};
```

```
A::value = 20
B::value = 10
```

같은 이름의 멤버가 있을 때 자식이 부모의 멤버를 가린다. 하지만 `B::value`처럼 명시적으로 부모 것을 사용할 수도 있다.

---

## 정적 바인딩과 함수 shadowing

```cpp
class B {
public:
    void print() {
        std::cout << "B::print()\\n";
    }
};

class A : public B {
public:
    void print() {
        std::cout << "A::print()\\n";
    }
};
```

```
Calling via B* ptr = &a:
B::print()
Calling via A object:
A::print()
```

함수도 변수처럼 shadowing이 일어난다. 포인터의 타입이 B니까 B의 print가 불린다. 정적 바인딩이기 때문이다.

---