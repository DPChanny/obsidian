---
---
`UFUNCTION()`은 `UCLASS` 내부에 선언된 C++ 멤버 함수를 단순한 내부 로직이 아닌, 언리얼 엔진의 다양한 시스템과 상호작용할 수 있는 **공식적인 '업무 능력' 또는 '기술'**로 등록하는 매크로입니다.

`UCLASS`가 객체의 '신분'을, `UPROPERTY`가 객체의 '소유물'을 정의한다면, `UFUNCTION`은 그 객체가 **'무엇을 할 수 있는지'**를 정의합니다. 이 매크로를 함수 선언 위에 붙이면, 언리얼 헤더 툴(UHT)은 이 함수를 블루프린트, 네트워크, 이벤트 시스템 등이 호출할 수 있도록 필요한 모든 연결 코드를 생성합니다.

#### UFUNCTION의 핵심 역할 (왜 사용하는가?)

1. **블루프린트에 C++ 기능 노출**: C++로 작성된 복잡하고 빠른 로직을 디자이너나 기획자가 블루프린트에서 블록(노드)으로 가져다 쓸 수 있게 합니다.
    
2. **커스텀 이벤트 제작**: C++에서 함수의 '이름'만 정의하고 실제 '구현'은 블루프린트에서 하도록 위임하거나, C++의 기본 기능에 블루프린트가 기능을 '추가'할 수 있게 합니다.
    
3. **네트워크 통신 (RPC)**: 멀티플레이어 게임에서 클라이언트가 서버의 함수를, 또는 서버가 클라이언트의 함수를 원격으로 호출(Remote Procedure Call)할 수 있게 합니다.
    
4. **델리게이트(Delegate)와 연동**: `OnHit`, `OnBeginOverlap`과 같은 이벤트가 발생했을 때 이 함수가 자동으로 호출되도록 '바인딩(Binding)'할 수 있습니다.
    
5. **에디터에서 직접 실행**: 게임을 실행하지 않고도 에디터의 `Details` 패널에서 버튼 클릭만으로 함수를 테스트할 수 있게 합니다.
    

---

### ■ 주요 지정자(Specifiers) 상세 설명

`UFUNCTION()` 괄호 안에 어떤 지정자를 넣느냐에 따라 함수의 역할과 성격이 완전히 달라집니다.

#### 1. 블루프린트 노출 및 이벤트 관련

|지정자|역할|주요 특징 및 사용처|
|---|---|---|
|**`BlueprintCallable`**|블루프린트에서 **실행 핀이 있는 노드로 호출**할 수 있게 합니다.|객체의 상태를 변경하는 대부분의 '행동' 함수에 사용됩니다. (예: `Attack()`, `OpenDoor()`, `TakeDamage()`)|
|**`BlueprintPure`**|블루프린트에서 **실행 핀이 없는 순수 노드**로 만듭니다. (Get 노드 형태)|객체의 상태를 변경하지 않고 값을 **읽어오기만** 하는 함수에 사용됩니다. (예: `GetHealth()`, `IsDead()`, `GetAmmoCount()`)|
|**`BlueprintImplementableEvent`**|C++에서는 **선언만** 하고, 실제 **구현은 블루프린트에서만** 합니다.|C++에서는 "이펙트를 터뜨려줘"라고 호출만 하고, 구체적으로 어떤 파티클과 사운드를 재생할지는 블루프린트에서 디자이너가 결정하게 할 때 사용합니다.|
|**`BlueprintNativeEvent`**|C++에 **기본 구현**을 두고, 블루프린트에서 **재정의(확장)**할 수 있습니다.|C++로 필수적인 로직(예: 데미지 계산)을 `_Implementation` 함수에 구현해두고, 블루프린트에서는 추가적인 시각 효과 등을 덧붙일 때 사용합니다.|

#### 2. 네트워크(RPC) 관련

|지정자|역할|주 사용처|
|---|---|---|
|**`Server`**|클라이언트에서 호출하여 **서버에서만 실행**되게 합니다.|플레이어가 총을 쏘거나 아이템을 사용하는 등, 게임 규칙에 영향을 주는 중요한 행동을 서버에 요청할 때.|
|**`Client`**|서버에서 호출하여 **특정 클라이언트에서만 실행**되게 합니다.|특정 플레이어에게만 보이는 UI(예: "미션 성공")를 띄우거나, 개인적인 효과를 재생할 때.|
|**`NetMulticast`**|서버에서 호출하여 **서버 자신과 모든 클라이언트에서 실행**되게 합니다.|폭발 이펙트와 사운드처럼, 게임에 참여한 모든 인원이 동시에 보고 들어야 하는 시각/청각적 효과를 동기화할 때.|
|**`Reliable`**|(위 지정자와 함께 사용) 이 RPC는 네트워크 상태가 나빠도 **반드시 전달됨**을 보장합니다.|총 발사, 죽음 처리 등 게임 플레이에 필수적인 RPC에 사용. (남용 시 네트워크 부하 증가)|
|**`Unreliable`**|(기본값) 네트워크 상황에 따라 RPC가 **유실될 수 있음**을 허용합니다.|캐릭터의 발소리처럼, 한두 번 유실되어도 게임에 큰 지장이 없는 비-필수적인 효과에 사용. (네트워크 부담이 적음)|

#### 3. 에디터 유틸리티

|지정자|역할|주 사용처|
|---|---|---|
|**`CallInEditor`**|에디터의 `Details` 패널에 이 함수를 실행하는 **버튼**을 만듭니다.|레벨에 배치된 액터의 상태를 리셋하거나, 절차적으로 메시를 생성하는 등 게임 실행 없이 테스트하고 싶을 때.|

---

### ■ 상황별 실전 예시 코드

#### 상황 1: 캐릭터의 기본 기능 정의

```cpp
// MyCharacter.h
UCLASS()
class AMyCharacter : public ACharacter
{
    GENERATED_BODY()

private:
    float Health = 100.f;

public:
    // 데미지를 입는 함수. 캐릭터의 상태를 변경하므로 Callable.
    UFUNCTION(BlueprintCallable, Category = "Character")
    void TakeDamage(float Amount);

    // 현재 체력을 %로 반환하는 함수. 상태를 변경하지 않으므로 Pure.
    UFUNCTION(BlueprintPure, Category = "Character")
    float GetHealthPercent() const;

    // 죽었을 때의 시각 효과. 구현은 블루프린트에서.
    UFUNCTION(BlueprintImplementableEvent, Category = "Character")
    void PlayDeathVFX();
};
```

#### 상황 2: 멀티플레이어 무기 발사 로직

```cpp
// MyWeapon.h
UCLASS()
class AMyWeapon : public AActor
{
    GENERATED_BODY()

public:
    // 1. 플레이어가 발사 키를 누르면 이 함수를 호출.
    UFUNCTION(BlueprintCallable, Category = "Weapon")
    void Fire();

protected:
    // 2. Fire 함수 내부에서 이 서버 RPC를 호출하여 실제 발사 로직을 서버에 요청.
    UFUNCTION(Server, Reliable)
    void Server_Fire();

    // 3. 서버가 발사 로직을 처리한 후, 총구 화염 같은 시각 효과를 모두에게 보여주기 위해 이 멀티캐스트 RPC를 호출.
    UFUNCTION(NetMulticast, Unreliable)
    void Multicast_PlayMuzzleFX();
};
```

#### 상황 3: 레벨 디자인 편의를 위한 액터

```cpp
// ProceduralGenerator.h
UCLASS()
class AProceduralGenerator : public AActor
{
    GENERATED_BODY()

public:
    // 에디터에서 이 버튼을 누르면 주변에 나무를 절차적으로 배치.
    UFUNCTION(CallInEditor, Category = "Generation")
    void GenerateTreesInArea();
};
```

이처럼 `UFUNCTION`은 단순한 함수 선언을 넘어, 지정자를 통해 함수의 역할과 실행 맥락(블루프린트, 네트워크, 에디터)을 정의하는 언리얼 엔진의 강력한 커뮤니케이션 도구입니다.