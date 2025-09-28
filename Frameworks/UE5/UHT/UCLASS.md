---
type: framework
---

`UCLASS()`는 평범한 C++ 클래스에 언리얼 엔진의 **시민권**을 부여하는 **'주민등록' 또는 '출생신고'** 절차와 같은 역할을 하는 매크로입니다. 이 매크로를 클래스 선언 위에 붙이는 순간, 언리얼 헤더 툴(UHT)이 해당 클래스를 인지하고, 언리얼 엔진의 강력한 오브젝트 시스템(UObject System)의 일원으로 정식 등록합니다.

`UCLASS()`가 없는 클래스는 엔진의 입장에서 존재하지 않는 것이나 마찬가지이며, 엔진의 어떤 기능과도 상호작용할 수 없습니다.

#### UCLASS의 핵심 역할 (왜 사용하는가?)

1. **리플렉션 (Reflection) 시스템 등록**: 엔진이 런타임에 클래스의 구조(변수, 함수, 부모 클래스 등)를 파악할 수 있게 합니다. 이는 아래의 모든 기능의 기반이 됩니다.
    
2. **자동 가비지 컬렉션 (GC)**: 클래스의 인스턴스(객체)가 더 이상 필요 없어지면 엔진이 자동으로 메모리를 회수하여, 복잡한 메모리 관리를 대신해 줍니다.
    
3. **블루프린트 연동**: C++ 클래스를 블루프린트에서 상속받아 확장하거나, 변수의 타입으로 사용하는 등 완벽한 통합을 지원합니다.
    
4. **에디터 연동**: 클래스의 인스턴스(액터 등)를 레벨에 배치하거나, 데이터 에셋으로 만드는 등 에디터에서 시각적으로 다룰 수 있게 합니다.
    
5. **직렬화 (Serialization)**: 클래스 객체의 상태를 파일(레벨, 에셋 등)에 저장하고 다시 불러올 수 있게 합니다.
    
6. **네트워킹 (Networking)**: 멀티플레이어 환경에서 객체를 서버와 클라이언트 간에 복제할 수 있는 기반을 제공합니다.
    

`UCLASS()` 매크로를 붙이는 것만으로도 클래스는 언리얼 오브젝트 시스템에 등록되어 다음과 같은 핵심 관리 기능의 혜택을 받습니다.

1. **가비지 컬렉션 (Garbage Collection)**
    
    - 엔진이 해당 클래스의 객체(인스턴스)의 메모리를 자동으로 관리합니다. 더 이상 필요 없을 때 알아서 제거해주므로 메모리 누수를 방지합니다.
        
2. **리플렉션 (Reflection)**
    
    - `Cast<>`나 `IsA<>` 같은 C++ 레벨에서의 타입 확인, 클래스 정보 조회 등 엔진의 런타임 타입 정보 시스템을 사용할 수 있습니다.
        
3. **직렬화 (Serialization)**
    
    - 이 클래스의 인스턴스가 다른 `UPROPERTY`에 의해 참조될 경우, 해당 객체와 그 속성들이 레벨이나 에셋 파일과 함께 저장되고 로드될 수 있습니다.
---

### ■ 주요 지정자(Specifiers) 상세 설명

`UCLASS()` 괄호 안에 어떤 지정자를 넣느냐에 따라 클래스의 역할과 동작이 결정됩니다.

|지정자|역할|주 사용처|
|---|---|---|
|**`Blueprintable`**|이 C++ 클래스를 **부모로 하는 블루프린트 클래스**를 만들 수 있습니다.|`ACharacter`를 기반으로 `BP_PlayerCharacter`를 만드는 것처럼, C++로 뼈대를 만들고 디자이너가 블루프린트에서 세부 사항을 구현하게 할 때.|
|**`BlueprintType`**|이 C++ 클래스를 블루프린트의 **변수 타입**으로 사용할 수 있습니다.|`UItemData`라는 C++ 클래스를 만들고, 블루프린트에서 `ItemData` 타입의 변수에 아이템 정보를 담아 전달해야 할 때.|
|**`Abstract`**|이 클래스는 직접 인스턴스화할 수 없으며, **반드시 자식 클래스에서 상속받아 사용**해야 합니다.|`ABaseWeapon`이라는 추상 클래스에 `Attack()` 함수 인터페이스만 정의하고, `ARifle`, `APistol` 등 구체적인 자식 클래스에서 실제 기능을 구현할 때.|
|**`NotBlueprintable`**|`Blueprintable`과 반대로, 블루프린트에서 이 클래스를 상속받는 것을 **금지**합니다.|게임의 핵심 시스템과 같이 C++에서만 완결되어야 하고, 블루프린트에서의 임의적인 변경을 막고 싶을 때.|
|**`ClassGroup = "그룹명"`**|에디터의 클래스 생성 메뉴에서 지정된 **그룹**에 이 클래스를 표시하여 가독성을 높입니다.|`ClassGroup=(Custom)` `ClassGroup=(AI)` `ClassGroup=(UI)` 등으로 커스텀 클래스를 깔끔하게 정리할 때.|

#### 4. 기타 유용한 지정자

|지정자|역할|
|---|---|
|**`meta = ( ... )`**|부가적인 메타데이터를 제공하여 에디터의 동작을 제어합니다.|
|`meta = (BlueprintSpawnableComponent)`|`UActorComponent` 파생 클래스의 경우, 블루프린트의 `Add Component` 목록에 나타나게 합니다. **(컴포넌트에는 거의 필수)**|
|`meta = (DisplayName = "표시 이름")`|에디터에 표시될 클래스의 이름을 바꿉니다.|
|**`Config = "ConfigFileName"`**|클래스의 특정 `UPROPERTY`를 `.ini` 설정 파일과 연동하여 값을 관리합니다.|

---

### ■ 상황별 실전 예시 코드

#### 상황 1: 상호작용 가능한 문 액터의 기반 클래스

- **요구사항**: C++로 문의 기본 기능(열기/닫기)을 만들고, 디자이너가 이를 상속받아 다양한 종류의 문(나무문, 철문 등) 블루프린트를 만들 수 있어야 합니다.
    

```cpp
// BaseInteractiveDoor.h
UCLASS(Blueprintable, ClassGroup=(Interactables))
class ABaseInteractiveDoor : public AActor
{
    GENERATED_BODY()

public:
    // 문을 여는 기능. 블루프린트에서 구현하거나 C++ 함수를 호출할 수 있음.
    UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category = "Door Interaction")
    void OpenDoor();

    // 블루프린트에서 구현할 OpenDoor 함수의 C++ 기본 구현
    virtual void OpenDoor_Implementation(); 
};
```

- **해설**: `Blueprintable`로 설정하여 `BP_WoodenDoor`, `BP_SlidingDoor` 등의 자식 블루프린트 생성을 허용했습니다. `UFUNCTION`의 `BlueprintNativeEvent`는 블루프린트에서 이 함수를 재정의(Override)하여 각 문마다 다른 열림 애니메이션을 재생하게 하면서, 동시에 C++의 기본 로직도 가질 수 있게 해줍니다.
    

#### 상황 2: 어떤 액터에든 부착 가능한 '타겟팅' 기능 컴포넌트

- **요구사항**: 플레이어나 포탑 등 어떤 액터든 이 컴포넌트를 부착하면 주변의 적을 탐지하는 기능을 갖게 하고 싶습니다.
    

```cpp
// TargetDetectorComponent.h
UCLASS(ClassGroup=(Custom), meta=(BlueprintSpawnableComponent))
class UTargetDetectorComponent : public UActorComponent
{
    GENERATED_BODY()

public:
    // 탐지 반경. 디자이너가 각 액터마다 다른 값으로 설정 가능.
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Targeting")
    float DetectionRadius = 1000.f;

    // 가장 가까운 타겟을 찾아 반환하는 함수. 블루프린트에서 사용.
    UFUNCTION(BlueprintPure, Category = "Targeting")
    AActor* FindClosestTarget();
};
```

- **해설**: `meta=(BlueprintSpawnableComponent)` 덕분에 이 컴포넌트는 모든 액터의 블루프린트 에디터 내 `Add Component` 목록에 나타납니다. `ClassGroup=(Custom)`은 이 목록을 'Custom' 카테고리로 묶어줍니다.
    

#### 상황 3: 데이터만 담는 아이템 정보 에셋

- **요구사항**: 게임에 등장하는 수많은 아이템(포션, 검, 갑옷 등)의 정보를 담는 데이터 템플릿이 필요합니다. 월드에 배치되지는 않지만, 에디터에서 아이템 에셋을 쉽게 생성하고 관리하고 싶습니다.
    

```cpp
// ItemData.h
#include "Engine/DataAsset.h"

UCLASS(BlueprintType)
class UItemData : public UPrimaryDataAsset
{
    GENERATED_BODY()

public:
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Item Properties")
    FText ItemName;

    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Item Properties")
    TObjectPtr<UTexture2D> ItemIcon;

    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Item Properties")
    bool bIsStackable;
};
```

- **해설**: `UDataAsset`을 상속하고 `BlueprintType`으로 지정했습니다. 이 클래스는 `Blueprintable`이 아니므로 상속받아 확장할 수는 없지만, 블루프린트에서 `ItemData` 타입의 변수를 만들어 이 에셋을 담을 수 있습니다. 콘텐츠 브라우저에서 우클릭하여 `DA_HealthPotion`과 같은 데이터 에셋을 직접 생성할 수 있어 데이터 관리에 매우 용이합니다.
    

이처럼 `UCLASS`는 단순히 클래스를 선언하는 것을 넘어, **'이 클래스가 엔진과 에디터, 그리고 다른 개발자들과 어떻게 상호작용할 것인가'**를 정의하는 강력하고 필수적인 설계 도구입니다.