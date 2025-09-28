---
type: language
related:
  - "[[Generals/OOP/Class|Class]]"
---
## 멤버 변수와 멤버 함수

클래스는 **데이터를 저장하는 멤버 변수**와 **기능을 수행하는 멤버 함수**로 구성된다.

멤버 변수는 객체 상태를 나타내고, 멤버 함수는 객체가 수행할 수 있는 동작을 정의한다.

```cpp
#include <iostream>
using namespace std;

class Player {
private:
    int hp; // 멤버 변수
public:
    void setHP(int value) { // 멤버 함수
        hp = value;
    }

    void showHP() const {
        cout << "HP: " << hp << endl;
    }
};
```

---

## 상수 멤버 함수

멤버 함수 뒤에 `const`를 붙이면, **객체의 상태를 변경하지 않는 함수임을 명시**할 수 있다.

이는 `const 객체`에서도 호출할 수 있으며, 내부 멤버 수정을 막는다.

```cpp
void showHP() const {
    // this->hp = 10; // 컴파일 에러
    cout << "HP: " << hp << endl;
}
```

---

## 외부 정의 방식 (함수 선언과 정의 분리)

함수 구현을 클래스 외부로 빼낼 수 있다.

이는 **헤더 파일 - 소스 파일 분리 구조**에 필수적인 스타일이다.

```cpp
class Monster {
private:
    int level;
public:
    void setLevel(int value);     // 선언만
    void printLevel() const;
};

// 클래스 외부에서 정의
void Monster::setLevel(int value) {
    level = value;
}

void Monster::printLevel() const {
    cout << "Level: " << level << endl;
}
```

---

## 생성자와 소멸자

### 생성자 (Constructor)

- 클래스의 객체가 **생성될 때 자동으로 호출되는 함수**
- **클래스 이름과 동일한 이름을 가지며 반환형이 없다**
- 오버로딩 가능
- 멤버 초기화를 `initializer list`로 처리할 수 있다

```cpp
class Weapon {
private:
    int damage;
public:
    Weapon() : damage(10) {
        cout << "기본 생성자 호출\\n";
    }

    Weapon(int d) : damage(d) {
        cout << "매개변수 생성자 호출\\n";
    }

    void print() const {
        cout << "Damage: " << damage << endl;
    }
};
```

### 소멸자 (Destructor)

- 객체가 **삭제될 때 자동 호출되는 함수**
- `~클래스이름()` 형식으로 정의
- 보통 리소스 정리, 로그 등에 사용

```cpp
class Weapon {
    // ...
    ~Weapon() {
        cout << "소멸자 호출\\n";
    }
};
```

---

## 상수 멤버 변수 (`const`), 정적 멤버 변수 (`static`)

- `const` 멤버 변수는 초기화 후 변경 불가. 생성자에서만 초기화 가능
- `static`은 객체에 속하지 않고, 클래스 전체에서 공유됨

```cpp
class Item {
private:
    const int id;
    static int count;
public:
    Item(int i) : id(i) { count++; }

    void printID() const {
        cout << "ID: " << id << endl;
    }

    static void printCount() {
        cout << "Total: " << count << endl;
    }
};

int Item::count = 0;
```

## const 객체에서의 동작

- `const` 객체는 **멤버 데이터를 변경할 수 없기 때문에**, **`const` 멤버 함수만 호출 가능하다**
- `const` 멤버 함수는 함수 뒤에 `const`를 붙여야 한다

```cpp
class Tool {
private:
    int durability;
public:
    Tool(int d) : durability(d) {}

    void use() {
        durability--;
    }

    void info() const {
        cout << "Durability: " << durability << endl;
    }
};

int main() {
    const Tool hammer(100);
    // hammer.use();      // 오류: const 객체에서 비-const 함수 호출 불가
    hammer.info();        // OK: const 함수이므로 호출 가능
}
```

- 여기서 `info()`는 `const` 함수로 선언되었기 때문에, `const Tool` 객체에서 호출 가능하다
- 반대로 `use()`는 상태를 바꾸므로 호출 불가능

---

## 실행 예제 전체

```cpp
#include <iostream>
using namespace std;

class Player {
private:
    int hp;
public:
    void setHP(int value) {
        hp = value;
    }

    void showHP() const {
        cout << "HP: " << hp << endl;
    }
};

class Monster {
private:
    int level;
public:
    void setLevel(int value);
    void printLevel() const;
};

void Monster::setLevel(int value) {
    level = value;
}

void Monster::printLevel() const {
    cout << "Level: " << level << endl;
}

class Item {
private:
    const int id;
    static int count;
public:
    Item(int i) : id(i) { count++; }
    void printID() const {
        cout << "ID: " << id << endl;
    }
    static void printCount() {
        cout << "Total: " << count << endl;
    }
};

int Item::count = 0;

int main() {
    Player p;
    p.setHP(100);
    p.showHP();

    Monster m;
    m.setLevel(5);
    m.printLevel();

    Item a(1), b(2);
    a.printID();
    b.printID();
    Item::printCount();
}
```

**실행 결과:**

```
HP: 100
Level: 5
ID: 1
ID: 2
Total: 2
```

---

## 정리

C++ 클래스는 상태(멤버 변수)와 동작(멤버 함수)을 결합한 구조로, 접근 제한자를 통해 외부와의 경계를 설정하고, 상수화나 외부 정의를 통해 보다 견고하고 유연한 코드를 작성할 수 있도록 한다.

이러한 구성은 객체지향의 기본이며, 클래스 단위의 모듈화와 인터페이스 설계에 있어서 핵심적인 기초가 된다.