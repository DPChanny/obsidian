C++에서 클래스의 멤버 변수를 초기화하는 방법은 크게 세 가지가 있습니다: **클래스 선언부(헤더)에서 직접 초기화**, **생성자 초기화 리스트(Initializer List)**, 그리고 **생성자 본문(Constructor Body)에서의 대입**입니다. 이 세 가지 방법은 실행되는 순서가 명확하게 정해져 있으며, 각각의 용도와 장단점이 뚜렷하여 상황에 맞게 사용하는 것이 중요합니다.

### 멤버 변수 초기화의 정확한 순서

객체가 생성될 때 멤버 변수의 초기화는 다음과 같은 **엄격한 순서**를 따릅니다. 이 순서를 이해하는 것이 가장 중요합니다.

1. **상속 관계에서의 순서:**
    
    - **가상 기본 클래스 생성자:** 가장 먼저 호출됩니다.
        
    - **일반 기본 클래스 생성자:** 가상 기본 클래스 이후에 호출됩니다. (여러 개일 경우 상속 선언 순서에 따름)
        
2. **멤버 변수 초기화 (가장 중요한 부분):**
    
    - **클래스에 선언된 순서대로** 멤버 변수들이 초기화됩니다. **생성자 초기화 리스트에 나열된 순서가 아님**에 반드시 유의해야 합니다.
        
    - 이때, 어떤 초기화 방법을 사용할지에 대한 우선순위는 다음과 같습니다.
        
        1. **생성자 초기화 리스트**에 해당 멤버가 명시되어 있다면, 해당 리스트의 값으로 **초기화**됩니다.
            
        2. 초기화 리스트에 없다면, **클래스 선언부에서 직접 초기화**한 값이 사용되어 **초기화**됩니다. (C++11 이상)
            
        3. 초기화 리스트에도 없고 클래스 선언부 초기화도 없다면, 해당 멤버는 **기본 초기화(default initialization)** 됩니다. (예: `int`는 쓰레기 값, `std::string`은 빈 문자열)
            
3. **생성자 본문(Constructor Body) 실행:**
    
    - 모든 멤버 변수의 '초기화'가 완료된 후, 생성자의 `{}` 안에 있는 코드들이 실행됩니다. 이 단계에서 이루어지는 것은 엄밀히 말해 **'초기화(Initialization)'가 아닌 '대입(Assignment)'**입니다.
        
**핵심 요약:** 멤버 변수는 **선언된 순서대로**, **초기화 리스트 > 클래스 선언부 초기화 > 기본 초기화** 순서의 우선순위를 적용받아 **'초기화'**가 먼저 완료되고, 그 후에 생성자 본문의 **'대입'** 연산이 실행됩니다.

---

### 초기화 방법별 상세 설명 및 사용처

#### 1. 클래스 선언부(헤더)에서 직접 초기화 (In-Class Member Initializer)

C++11부터 도입된 편리한 기능입니다. 클래스를 선언할 때 멤버 변수에 직접 초기값을 지정할 수 있습니다.

```cpp
class Player {
private:
    int level = 1; // 클래스 선언부에서 직접 초기화
    float hp = 100.0f;
    std::string name = "Unknown";
    bool is_active = true;
};
```

- **실행 시점:** 생성자 초기화 리스트 이전에 실행됩니다.
    
- **주요 사용처:**
    
    - 모든 생성자에서 공통적으로 사용되는 **기본값**을 지정할 때 매우 유용합니다.
        
    - 코드가 간결해지고, 멤버 변수의 기본 상태를 클래스 선언부에서 한눈에 파악할 수 있습니다.
        
    - 상수(`const`)가 아닌 멤버들의 기본값을 설정하는 데 적합합니다.
        

#### 2. 생성자 초기화 리스트 (Member Initializer List)

생성자 선언부의 `()` 뒤에 콜론(`:`)을 붙여 멤버 변수를 초기화하는 방식입니다. 진정한 의미의 **'초기화'**가 일어나는 곳입니다.

```cpp
class Player {
private:
    int level;
    const float max_hp; // const 멤버
    std::string& character_name; // 참조 멤버

public:
    Player(int start_level, std::string& name)
        : level(start_level),      // '초기화'
          max_hp(100.0f * start_level), // '초기화'
          character_name(name)      // '초기화'
    {
        // 생성자 본문
    }
};
```

- **실행 시점:** 클래스 선언부 초기화 이후, 생성자 본문 이전에 실행됩니다.
    
- **주요 사용처 및 장점:**
    
    - **반드시 사용해야 하는 경우:**
        
        - `const` 멤버 변수: `const`는 선언과 동시에 초기화되어야 하므로, 생성자 본문에서 대입이 불가능합니다.
            
        - 참조(`&`) 멤버 변수: 참조 역시 선언과 동시에 대상을 지정해야 합니다.
            
        - 기본 생성자가 없는 클래스 타입의 멤버 변수를 초기화할 때.
            
    - **성능상 이점:** 생성자 본문에서의 대입은 `기본 생성자 호출(초기화) -> 대입 연산자 호출(대입)`의 2단계를 거치지만, 초기화 리스트는 `복사 생성자 호출(초기화)` 1단계로 끝납니다. 특히 멤버 변수가 무거운 클래스 객체일 경우 성능 차이가 발생할 수 있습니다.
        
    - **가장 표준적이고 권장되는 초기화 방식**입니다.
        

**주의할 점:** 초기화 리스트에 나열된 순서가 아니라, **클래스에 멤버 변수가 선언된 순서대로 초기화가 진행**됩니다. 아래와 같은 코드는 위험합니다.

```cpp
class BadExample {
private:
    int a;
    int b;
public:
    // b가 a보다 먼저 선언되었으므로, b가 먼저 초기화된다.
    // 따라서 b는 초기화되지 않은 a의 값(쓰레기 값)으로 초기화될 수 있다.
    BadExample(int val) : b(val), a(b) {}
};
```

#### 3. 생성자 본문(Constructor Body)에서의 대입

생성자의 `{}` 안에서 멤버 변수에 값을 할당하는 가장 일반적인 코드 작성 방식과 유사합니다.

```cpp
class Player {
private:
    int level;
    float hp;

public:
    Player(int start_level) {
        level = start_level; // '대입'
        if (level > 10) {
            hp = 200.0f;     // '대입'
        } else {
            hp = 100.0f;     // '대입'
        }
    }
};
```

- **실행 시점:** 모든 멤버의 '초기화'가 끝난 후 마지막에 실행됩니다.
    
- **주요 사용처:**
    
    - 초기화 과정에 **복잡한 로직(조건문, 반복문 등)이 필요**할 때 사용합니다.
        
    - 멤버 변수들을 조합하여 다른 멤버를 설정해야 하는 등, 단순 초기화로 불가능한 작업을 수행할 때 유용합니다.
        
    - 파일을 읽어오거나, 예외 처리가 필요한 초기화 작업을 수행할 때 사용됩니다.
        

### 종합 예제 및 실행 순서 분석

```cpp
#include <iostream>
#include <string>

class Component {
public:
    Component(const std::string& name) {
        std::cout << "  Component '" << name << "' 생성자 본문 실행\n";
    }
};

class GameObject {
private:
    int object_id = 0;                  // 1. 클래스 선언부 초기화 (기본값)
    const int type_id;                  // const 멤버
    std::string name;
    Component mesh{"Default Mesh"};   // 1. 클래스 선언부 초기화

public:
    GameObject(int id, int t_id, const std::string& n)
        : type_id(t_id),            // 2. 초기화 리스트 (type_id)
          name(n)                   // 2. 초기화 리스트 (name)
    {
        std::cout << "GameObject 생성자 본문 시작\n";
        this->object_id = id;       // 3. 생성자 본문에서 대입
        std::cout << "GameObject 생성자 본문 종료\n";
    }

    void PrintInfo() {
        std::cout << "\n--- Object Info ---\n"
                  << "ID: " << object_id << "\n"
                  << "Type ID: " << type_id << "\n"
                  << "Name: " << name << "\n"
                  << "-------------------\n";
    }
};

int main() {
    std::cout << "main 함수 시작\n";
    GameObject player(1001, 1, "PlayerCharacter");
    player.PrintInfo();
    std::cout << "main 함수 종료\n";
    return 0;
}
```

**위 코드의 실행 순서:**

1. `main` 함수 시작
    
2. `GameObject player` 객체 생성 시작
    
3. **멤버 변수 초기화 단계 (선언된 순서대로):**
    
    - `object_id`: 클래스 선언부에 의해 `0`으로 **초기화**.
        
    - `type_id`: 초기화 리스트의 `type_id(t_id)`에 의해 `1`로 **초기화**.
        
    - `name`: 초기화 리스트의 `name(n)`에 의해 `"PlayerCharacter"`로 **초기화**.
        
    - `mesh`: 클래스 선언부의 `Component mesh{"Default Mesh"}`에 의해 `Component`의 생성자가 호출됨.
        
        - "Component 'Default Mesh' 생성자 본문 실행" 출력.
            
4. **생성자 본문 실행 단계:**
    
    - "GameObject 생성자 본문 시작" 출력.
        
    - `this->object_id = id;` 코드가 실행되어, `object_id`의 값이 `0`에서 `1001`로 **대입**됨.
        
    - "GameObject 생성자 본문 종료" 출력.
        
5. `player.PrintInfo()` 호출 및 정보 출력.
    
6. `main` 함수 종료.
    

### 결론: 최선의 사용법 (Best Practice)

- **기본값은 클래스 선언부에서 초기화**하여 코드의 가독성을 높이세요.
    
- **생성자에서는 초기화 리스트를 사용하는 것을 기본 원칙**으로 삼으세요. 이는 성능상 이점을 제공하며, `const`나 참조 멤버를 초기화하는 유일한 방법입니다.
    
- 초기화 과정에 **복잡한 로직이 필요할 때만 생성자 본문에서의 대입을 사용**하세요.
