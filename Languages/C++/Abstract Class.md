---
type: language
parent: "[[C++]]"
related:
  - "[[Generals/OOP/Abstraction/Abstract Class|Abstract Class]]"
---
## 정의 방식

- 추상 클래스는 **하나 이상의 순수 가상 함수(pure virtual function)**를 포함한다.
- 순수 가상 함수는 함수 선언 뒤에 `= 0`을 붙여 정의한다.

```cpp
class Interface {
public:
    virtual void func() = 0;
};
```

- 이 클래스는 **직접 인스턴스화 불가**. 컴파일 타임에 `new Interface()` 또는 객체 선언 시 에러 발생.

---

## 소멸자 주의사항

- 추상 클래스에는 **반드시 가상 소멸자 `virtual ~Class()`를 선언**해야 한다.
- 그래야 **파생 클래스 객체를 부모 포인터로 delete할 때 정리 순서가 보장**된다.

## 비순수 가상 함수가 포함된 추상 클래스

- 추상 클래스는 **하나 이상의 순수 가상 함수만 포함해야 한다는 제한은 없다.**
- 즉, **순수 가상 함수와 일반 함수(구현된 가상 함수 또는 일반 함수)를 동시에 가질 수 있다.**
- 이런 경우, 이 클래스는 **"인터페이스"가 아닌 "기본 동작을 일부 포함한 추상 클래스"**라고 보는 게 맞다.

```cpp
cpp
복사편집
class AbstractWorker {
public:
    virtual ~AbstractWorker() {}

    // 순수 가상 함수: 반드시 파생 클래스가 구현해야 함
    virtual void work() = 0;

    // 비순수 가상 함수: 공통 기본 동작 제공
    virtual void log() {
        std::cout << "AbstractWorker logging\\n";
    }
};

```

- 이 클래스는 여전히 추상 클래스이므로 직접 인스턴스화할 수 없다.
- 하지만 `log()` 함수는 기본 동작을 포함하고 있고, 파생 클래스가 굳이 오버라이드하지 않아도 된다.
- 즉, **인터페이스처럼 동작을 강제하면서도 일부 공통 동작을 공유**할 수 있는 형태다.

---

## 정리 포인트

- C++에서 **"인터페이스"는 보통 모든 함수가 `= 0`으로 정의된 추상 클래스**를 의미한다.
- **"추상 클래스"는 그보다 더 넓은 개념으로**, 순수 가상 함수와 일반 멤버 함수를 혼합할 수 있다.
- 이 구조를 통해 C++은 **기능 명세(interface)**와 **기본 구현 공유(abstract base class)**를 **하나의 언어 구조로 통합**한다.