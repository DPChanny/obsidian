---
type: framework
---

#### UINTERFACE의 핵심 역할 (왜 사용하는가?)

1. **기능의 추상화 및 표준화**: "상호작용 하려면 `OnInteract()` 함수가 있어야 한다" 와 같이, 특정 기능에 필요한 함수들의 **'설계도'** 또는 **'목록'**을 정의합니다. 실제 구현은 각 클래스가 알아서 하도록 맡깁니다.
    
2. **느슨한 결합 (Loose Coupling)**: **가장 강력한 장점입니다.** 플레이어가 어떤 물체와 상호작용할 때, 그 물체가 '문'인지 '상자'인지 일일이 확인할 필요가 없습니다. 그저 "이 물체는 `상호작용` 인터페이스를 가지고 있는가?" 하나만 확인하고, 가지고 있다면 `OnInteract()` 함수를 호출하면 됩니다. 이로 인해 시스템이 매우 유연해지고 확장성이 좋아집니다.
    
3. **다중 상속의 대안**: 언리얼의 `UCLASS`는 단 하나의 부모 클래스만 가질 수 있지만, **인터페이스는 여러 개를 동시에 가질(구현할) 수 있습니다.** 예를 들어 한 캐릭터가 '상호작용 가능' 인터페이스와 '전투 가능' 인터페이스, '대화 가능' 인터페이스를 모두 가질 수 있습니다.
    

---

### ■ UINTERFACE의 독특한 2중 구조

`UINTERFACE`를 선언할 때는 항상 `U`로 시작하는 클래스와 `I`로 시작하는 클래스, 두 개가 한 쌍으로 만들어집니다. 이는 매우 헷갈릴 수 있지만, 각자의 역할이 명확합니다.

1. **`U...` 클래스 (예: `UInteractableInterface`)**
    
    - 언리얼 엔진과 블루프린트를 위한 **'껍데기' 또는 '대리인'**입니다.
        
    - 리플렉션 시스템이 인식하고, 가비지 컬렉션이 관리할 수 있도록 하는 최소한의 장치입니다. 이 클래스 자체에는 보통 아무 코드도 작성하지 않습니다.
        
2. **`I...` 클래스 (예: `IInteractableInterface`)**
    
    - C++ 코드에서 **실제로 상속받고 사용하는 '진짜' 인터페이스**입니다.
        
    - 우리가 정의하고 싶은 함수들이 바로 이 클래스 안에 선언됩니다.
        

**요약: `U-`클래스는 엔진용, `I-`클래스는 C++ 프로그래머용입니다.**

---

### ■ 주요 지정자(Specifiers) 상세 설명

|지정자|역할|**기본값과의 차이**|주 사용처|
|---|---|---|---|
|**`Blueprintable`**|이 인터페이스를 블루프린트에서 구현하고 사용할 수 있게 합니다.|**블루프린트 연동 기능 활성화**|대부분의 인터페이스는 C++뿐만 아니라 블루프린트에서도 활용되므로 거의 필수로 사용됩니다.|
|**`MinimalAPI`**|클래스 정보를 외부에 최소한으로만 노출하여 컴파일/링크 시간을 줄입니다.|**API 노출 최소화**|다른 모듈에서 이 인터페이스를 직접 참조할 일이 없을 때 사용하는 최적화 옵션입니다.|

---

### ■ 상황별 실전 예시: '상호작용' 시스템 전체 구현

#### 1단계: 인터페이스 정의 (`InteractableInterface.h`)

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "InteractableInterface.generated.h"

// 1. U-클래스 (엔진용 껍데기)
UINTERFACE(MinimalAPI, Blueprintable)
class UInteractableInterface : public UInterface
{
    GENERATED_BODY()
};

// 2. I-클래스 (C++용 실제 인터페이스)
class YOURPROJECT_API IInteractableInterface
{
    GENERATED_BODY()

public:
    // 이 인터페이스를 구현하는 클래스는 반드시 OnInteract 함수를 가져야 합니다.
    // BlueprintNativeEvent: C++와 블루프린트 양쪽에서 모두 구현 가능
    UFUNCTION(BlueprintCallable, BlueprintNativeEvent, Category = "Interaction")
    void OnInteract(AActor* Interactor);
};
```

#### 2단계: 문(Door) 액터에 인터페이스 구현 (`MyDoor.h` & `.cpp`)

```cpp
// MyDoor.h
#include "InteractableInterface.h" // 인터페이스 헤더 포함

UCLASS()
class AMyDoor : public AActor, public IInteractableInterface // 인터페이스 상속
{
    GENERATED_BODY()

public:
    // 인터페이스 함수를 실제로 구현
    virtual void OnInteract_Implementation(AActor* Interactor) override;
};

// MyDoor.cpp
#include "MyDoor.h"

void AMyDoor::OnInteract_Implementation(AActor* Interactor)
{
    // 부모 클래스의 기본 구현이 있다면 호출
    // IInteractableInterface::OnInteract_Implementation(Interactor);

    UE_LOG(LogTemp, Warning, TEXT("The door is opening!"));
    // ... 문 열리는 애니메이션 재생 로직 ...
}
```

#### 3단계: 보물상자(TreasureChest) 액터에 인터페이스 구현

```cpp
// TreasureChest.h
#include "InteractableInterface.h"

UCLASS()
class ATreasureChest : public AActor, public IInteractableInterface
{
    GENERATED_BODY()
public:
    virtual void OnInteract_Implementation(AActor* Interactor) override;
};

// TreasureChest.cpp
void ATreasureChest::OnInteract_Implementation(AActor* Interactor)
{
    UE_LOG(LogTemp, Warning, TEXT("You found treasure!"));
    // ... 아이템을 주거나, 파티클 이펙트를 터뜨리는 로직 ...
}
```

#### 4단계: 플레이어 캐릭터에서 인터페이스 사용 (`PlayerCharacter.cpp`)

```cpp
// PlayerCharacter.cpp
void APlayerCharacter::TryInteract()
{
    // ... 카메라 앞에서 Raycast를 쏘는 로직 ...
    FHitResult HitResult;
    // ... Raycast가 무언가에 맞았다면 ...
    
    AActor* HitActor = HitResult.GetActor();
    if (HitActor)
    {
        // 핵심: 맞은 액터가 '문'인지 '상자'인지 확인할 필요가 없습니다!
        // 오직 'IInteractableInterface' 자격증이 있는지(구현했는지)만 확인합니다.
        if (HitActor->Implements<UInteractableInterface>())
        {
            // Execute_ 접두사를 붙여 인터페이스 함수를 안전하게 호출합니다.
            // 블루프린트 구현을 포함하여 호출해주는 방식입니다.
            IInteractableInterface::Execute_OnInteract(HitActor, this);
        }
    }
}
```

이처럼 `UINTERFACE`를 사용하면, `TryInteract()` 함수는 앞으로 어떤 새로운 상호작용 객체가 추가되든 단 한 줄도 수정할 필요 없이 완벽하게 작동합니다. 이것이 바로 인터페이스가 제공하는 **느슨한 결합**의 강력함입니다.