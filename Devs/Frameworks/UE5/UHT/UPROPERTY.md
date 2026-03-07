---
---

`UPROPERTY()`는 C++ 멤버 변수를 단순한 변수가 아닌, 언리얼 엔진의 강력한 기능들과 상호작용할 수 있는 **'특별 관리 대상'**으로 만들어주는 매크로입니다. 이 매크로를 통해 변수는 엔진의 여러 시스템에 자신을 등록하게 됩니다.

#### UPROPERTY의 핵심 역할 (왜 사용하는가?)

1. **가비지 컬렉션 (GC) 방지**: `UObject` 파생 객체를 가리키는 포인터가 GC에 의해 임의로 삭제되는 것을 막아 크래시를 방지합니다. **(가장 중요한 이유)**
    
2. **에디터 연동**: 변수를 언리얼 에디터의 디테일 패널에 노출시켜 값을 보거나 편집할 수 있게 합니다.
    
3. **블루프린트 연동**: C++ 변수를 블루프린트에서 노드로 사용(읽기/쓰기)할 수 있게 합니다.
    
4. **자동 저장/로드**: 게임을 저장할 때 변수의 값을 함께 저장하고, 로드 시 복원합니다.
    
5. **네트워크 복제**: 멀티플레이어 게임에서 서버와 클라이언트 간의 변수 값을 동기화합니다.
    
#### UPROPERTY()의 기본 상태

아무 지정자 없이 `UPROPERTY()`만 덜렁 쓴다면, 다음과 같은 **최소한의 기능만 기본으로 활성화**됩니다.

- **(O) 가비지 컬렉션(GC) 대상에 포함**: 포인터 변수가 GC로부터 보호받습니다.
    
- **(O) 직렬화(저장/로드) 대상에 포함**: 변수의 값이 디스크에 저장되고 로드됩니다.
    
- **(X) 에디터 노출 안 됨**: `Details` 패널에 보이지 않습니다.
    
- **(X) 블루프린트 접근 불가**: 블루프린트에서 Get/Set 노드를 사용할 수 없습니다.
    
- **(X) 네트워크 복제 안 됨**: 멀티플레이어에서 동기화되지 않습니다.
    

따라서 `UPROPERTY()`만 쓰는 경우는 **"C++ 내부에서만 사용하는 UObject 포인터를 GC로부터 안전하게 보호하고 싶지만, 외부에 노출할 필요는 없을 때"** 입니다.

---

### ■ 주요 지정자(Specifiers) 상세 설명

`UPROPERTY()` 괄호 안에 어떤 지정자를 넣느냐에 따라 기능이 결정됩니다.

#### 1. 편집 가능성과 노출 (에디터 디테일 패널 제어)

|지정자|역할|주 사용처|
|---|---|---|
|**`VisibleAnywhere`**|값을 볼 수만 있고, 편집은 불가능합니다.|C++에서 생성한 컴포넌트 포인터, 게임 중에 변하는 현재 체력(`CurrentHealth`) 등 디버깅 목적으로 보고 싶을 때.|
|**`EditAnywhere`**|값을 보거나 편집할 수 있습니다. (기본값, 인스턴스 모두)|캐릭터의 최대 체력(`MaxHealth`)처럼 기본값을 가지지만, 특정 보스 몬스터만 값을 다르게 주고 싶을 때.|
|**`VisibleDefaultsOnly`**|블루프린트 **기본값** 창에서만 볼 수 있습니다.|클래스의 핵심 속성이지만 수정할 필요는 없을 때.|
|**`EditDefaultsOnly`**|블루프린트 **기본값** 창에서만 편집할 수 있습니다.|캐릭터의 '기본 이동 속도'처럼, 해당 클래스의 모든 인스턴스가 동일한 기본값을 가져야 할 때.|
|**`VisibleInstanceOnly`**|레벨에 배치된 **인스턴스**에서만 볼 수 있습니다.|레벨에 배치된 각 액터의 고유한 상태를 디버깅할 때.|
|**`EditInstanceOnly`**|레벨에 배치된 **인스턴스**에서만 편집할 수 있습니다.|각기 다른 순찰 경로를 가져야 하는 AI 경비병의 '경로'(`PatrolPath`) 변수처럼, 인스턴스별로 값이 반드시 달라야 할 때.|

#### 2. 블루프린트 접근 제어

|지정자|역할|주 사용처|
|---|---|---|
|**`BlueprintReadOnly`**|블루프린트에서 값을 **읽을 수만** 있습니다. (Get 노드)|C++에서 모든 계산을 마치고, 블루프린트는 그 결과를 가져다 UI에 표시하는 등의 용도로만 사용할 때.|
|**`BlueprintReadWrite`**|블루프린트에서 값을 **읽고 쓸 수** 있습니다. (Get/Set 노드)|블루프린트 레벨 스크립트 등에서 게임의 흐름에 따라 C++ 변수의 상태를 직접 바꿔야 할 때.|

#### 3. 네트워크 복제

|지정자|역할|주 사용처|
|---|---|---|
|**`Replicated`**|서버의 값이 변경되면 클라이언트로 자동 복제됩니다.|플레이어의 체력, 점수, 현재 위치 등 모든 플레이어가 알아야 하는 상태 정보.|
|**`ReplicatedUsing=함수이름`**|`Replicated`와 동일하지만, 값이 복제될 때마다 지정된 함수를 클라이언트에서 호출합니다.|체력이 바뀔 때마다 UI를 갱신하거나, 특정 상태가 될 때마다 이펙트를 재생하는 등 '값이 바뀌었을 때 무언가 해야 할 때'.|

#### 4. 기타 유용한 지정자

|지정자|역할|
|---|---|
|**`Category = "카테고리명"`**|디테일 패널에서 변수들을 지정된 카테고리 이름으로 묶어 가독성을 높입니다. (거의 필수)|
|**`Meta = ( ... )`**|추가적인 메타데이터를 제공합니다. (예: `ClampMin=0.0`, `UIMin=0.0` 으로 값의 범위를 제한)|
|**`Transient`**|`UPROPERTY` 임에도 불구하고 저장/로드 대상에서 제외합니다. (임시 값에 사용)|

---

### ■ 상황별 실전 예시 코드

#### 상황 1: 플레이어 캐릭터의 핵심 능력치 정의

```cpp
// MyPlayerCharacter.h
UCLASS()
class AMyPlayerCharacter : public ACharacter
{
    GENERATED_BODY()

private:
    // C++에서 생성한 핵심 컴포넌트. 디버깅을 위해 보기는 하되, 수정은 불가.
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    TObjectPtr<UCameraComponent> PlayerCamera;

    // 기획자가 기본값 및 인스턴스별로 최대 체력을 쉽게 조절하도록 함.
    // 게임 로직(블루프린트)에서는 이 값을 읽기만 함.
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Player Stats")
    float MaxHealth = 100.f;

    // 현재 체력은 게임 중에 변하며, 모든 클라이언트가 알아야 함 (UI 표시 등).
    // 디자이너가 임의로 수정하면 안 되므로 VisibleAnywhere 사용.
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, ReplicatedUsing=OnRep_CurrentHealth, Category = "Player Stats")
    float CurrentHealth;
    
    // 현재 체력이 복제될 때마다 호출될 함수
    UFUNCTION()
    void OnRep_CurrentHealth();
};
```

#### 상황 2: 열리고 닫히는 문 액터

```cpp
// InteractiveDoor.h
UCLASS()
class AInteractiveDoor : public AActor
{
    GENERATED_BODY()

private:
    // 문이 열리는 속도. 기본값을 설정하되, 특정 무거운 문은 더 느리게 조절 가능.
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Door Settings")
    float OpenSpeed = 2.0f;

    // 잠금 상태는 레벨에 배치된 문마다 다를 수 있음. EditInstanceOnly가 적합.
    // 게임 플레이 중에 열쇠를 찾으면 블루프린트에서 이 값을 바꿔야 함.
    UPROPERTY(EditInstanceOnly, BlueprintReadWrite, Replicated, Category = "Door Settings")
    bool bIsLocked = false;
    
    // 순수 C++ 내부 로직용 포인터. 외부에 노출할 필요 전혀 없음.
    // 오직 가비지 컬렉션 방지를 위해 빈 UPROPERTY() 사용.
    UPROPERTY()
    TObjectPtr<UTriggerVolume> OverlapVolume;
};
```

#### 상황 3: 무기 클래스의 기본 스펙

```cpp
// BaseWeapon.h
UCLASS()
class ABaseWeapon : public AActor
{
    GENERATED_BODY()

private:
    // 이 무기 '클래스'의 기본 데미지. 블루프린트 기본값 창에서만 설정.
    // "AK47"의 기본 데미지는 25, "SniperRifle"의 기본 데미지는 100 과 같이 설정.
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = "Weapon Stats")
    float BaseDamage = 25.f;

    // 총알 개수. 게임 중에 계속 변하며 블루프린트 UI에 표시되어야 함.
    UPROPERTY(BlueprintReadWrite, Replicated, Category = "Weapon Stats")
    int32 CurrentAmmo;

    // 최대 총알 개수. 디자이너가 무기 종류별로 기본값 창에서 설정.
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = "Weapon Stats")
    int32 MaxAmmo = 30;
};
```

이 가이드가 `UPROPERTY`를 이해하고 프로젝트에 맞게 효과적으로 사용하는 데 도움이 되기를 바랍니다. 핵심은 **'이 변수가 어떤 역할을 하고, 누구에게, 어디까지 노출되어야 하는가?'** 를 항상 생각하며 가장 제한적인 지정자를 선택하는 것입니다
.