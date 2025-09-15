---
type: language
related:
  - "[[Generals/OOP/Encapsulation|Encapsulation]]"
---
## 접근 제한자 기본

C++ 클래스 멤버는 다음과 같은 접근 제한자를 갖는다.

- **`public`**: 어디서든 접근 가능
- **`protected`**: 파생 클래스에서는 접근 가능, 외부에서는 불가
- **`private`**: 클래스 내부에서만 접근 가능

---

## 상속 시 접근성 변화

C++은 `public`, `protected`, `private` 세 가지 방식으로 상속할 수 있으며, 이에 따라 기반 클래스 멤버의 접근성이 파생 클래스에 어떻게 전달되는지가 달라진다.

|상속 방식|`public` 멤버|`protected` 멤버|`private` 멤버|
|---|---|---|---|
|`public` 상속|`public`로 유지|`protected`로 유지|접근 불가|
|`protected` 상속|`protected`로 변환|`protected`로 유지|접근 불가|
|`private` 상속|`private`로 변환|`private`로 변환|접근 불가|

> private 멤버는 어떤 방식으로도 직접 상속되지 않음. 간접 접근만 가능.

---

## 예제와 결과

### 1. `public` 상속 예제

```cpp
class Base {
public:
    void showPublic();
protected:
    void showProtected();
private:
    void showPrivate();
};

class Derived : public Base {
public:
    void access() {
        showPublic();     // OK
        showProtected();  // OK
        // showPrivate(); // Error
    }
};
```

- `showPublic()`은 외부 접근 가능
- `showProtected()`는 내부 접근 가능
- `showPrivate()`는 접근 불가

---

### 2. `protected` 상속 예제

```cpp
class Base {
public:
    void showPublic();
protected:
    void showProtected();
};

class Derived : protected Base {
public:
    void access() {
        showPublic();     // OK (이제 protected)
        showProtected();  // OK
    }
};
```

- `showPublic()`도 외부에선 접근 불가 (protected로 바뀜)

---

### 3. `private` 상속 예제

```cpp
class Base {
public:
    void showPublic();
protected:
    void showProtected();
};

class Derived : private Base {
public:
    void access() {
        showPublic();     // OK (이제 private)
        showProtected();  // OK
    }
};
```

- 외부에서는 어떤 것도 접근 불가
- 내부에서는 모두 접근 가능 (단, 기반의 private은 제외)

## friend 선언

`friend`는 특정 클래스나 함수에게 **private이나 protected 멤버에 접근할 수 있는 권한**을 부여한다. 캡슐화 원칙을 어느 정도 깨는 방식이지만, 예외적으로 필요한 경우에 쓰인다.

보통 다음의 상황에서 사용된다:

- 디버깅 또는 로깅 도구가 내부 정보를 필요로 할 때
- 두 클래스가 긴밀히 협조해야 할 때 (예: 연산자 오버로딩)

```cpp
#include <iostream>
using namespace std;

class Secret {
private:
    int data;
public:
    Secret(int val) : data(val) {}
    friend void reveal(const Secret& s); // 외부 함수에게 접근 권한 부여
};

void reveal(const Secret& s) {
    cout << "Secret data: " << s.data << endl;
}

int main() {
    Secret s(42);
    reveal(s);
}
```

결과:

```
Secret data: 42
```

이 경우 외부 함수 `reveal()`은 `Secret` 객체의 `private` 멤버에 직접 접근할 수 있다.

---

## getter와 setter

가장 일반적인 캡슐화 방식은 멤버를 `private`으로 두고, 접근이 필요한 경우에 한해 **getter/setter 함수를 public으로 제공**하는 것이다.

### 예제: 읽기 전용 속성

```cpp
class Account {
private:
    double balance;
public:
    Account(double initial) : balance(initial) {}
    double getBalance() const {
        return balance;
    }
};
```

이 경우 외부에서는 `getBalance()`로만 접근 가능하고, 값은 직접 변경할 수 없다.

### 예제: 읽기/쓰기 모두 허용

```cpp
class Account {
private:
    double balance;
public:
    Account(double initial) : balance(initial) {}
    double getBalance() const {
        return balance;
    }
    void setBalance(double newBalance) {
        if (newBalance >= 0)
            balance = newBalance;
    }
};
```

여기서 `setBalance()`에는 간단한 유효성 검사도 포함되어 있어, 직접 멤버에 접근하는 것보다 더 안전하다.