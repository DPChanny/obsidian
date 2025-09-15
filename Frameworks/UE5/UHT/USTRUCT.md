---
type: framework
---

`USTRUCT()`는 여러 관련 변수들을 하나의 의미 있는 단위로 묶어주는 **'데이터 소포(parcel)' 또는 '사용자 정의 양식(form)'**을 만드는 매크로입니다. C++의 일반 `struct` 키워드 위에 이 매크로를 붙이면, 언리얼 헤더 툴(UHT)이 해당 구조체를 인식하고 언리얼 엔진의 리플렉션 시스템에 등록합니다.

`FVector`(X, Y, Z 위치 값)나 `FRotator`(Pitch, Yaw, Roll 회전 값)처럼, 게임에 필요한 자신만의 데이터 묶음을 만들고 싶을 때 사용합니다.

#### USTRUCT의 핵심 역할 (왜 사용하는가?)

1. **데이터 그룹화**: 여러 변수를 논리적인 하나의 단위로 묶어 코드의 가독성과 관리 편의성을 높입니다. (예: `FPlayerInfo` 구조체 안에 `PlayerName`, `Level`, `Exp`를 함께 관리)
    
2. **블루프린트 연동**: C++에서 정의한 구조체를 블루프린트에서 새로운 **변수 타입**으로 사용하거나, 함수의 입력/출력 핀으로 사용할 수 있게 합니다.
    
3. **에디터 연동**: `UCLASS` 내에서 `UPROPERTY`로 선언된 `USTRUCT` 변수는 `Details` 패널에 깔끔하게 그룹화되어 표시되며, 내부 멤버(`FPROPERTY`)들을 쉽게 편집할 수 있습니다.
    
4. **데이터 테이블 활용**: 데이터 테이블의 행(Row) 구조를 정의하는 데 사용되어, 게임 데이터를 엑셀과 같은 외부 파일로 관리하는 데이터 기반(Data-Driven) 설계를 가능하게 합니다.
    

#### USTRUCT()의 기본 상태

아무 지정자 없이 `USTRUCT()`만 선언하면, 이 구조체는 **C++ 코드 내에서만 사용할 수 있는 단순한 데이터 묶음**이 됩니다.

- **(O) 리플렉션 시스템에 등록**: C++ 레벨에서의 기본적인 리플렉션 기능(예: 복사, 비교)과 직렬화(저장/로드)가 가능해집니다.
    
- **(X) 블루프린트에서 사용 불가**: 블루프린트에서 이 구조체 타입의 변수를 만들거나, 구조체를 'Break'/'Make'하는 노드를 사용할 수 없습니다.
    

즉, `USTRUCT()`의 기본 상태는 **"엔진의 관리는 받지만 블루프린트에는 노출되지 않는 상태"**입니다.

---

### ■ 주요 지정자(Specifiers) 상세 설명 (기본값과 비교)

`USTRUCT`는 `UCLASS`보다 지정자가 훨씬 적으며, 가장 핵심적인 지정자는 `BlueprintType`입니다.

|지정자|역할|**기본값과의 차이**|주 사용처|
|---|---|---|---|
|**`BlueprintType`**|블루프린트 시스템에 이 구조체를 새로운 **변수 타입**으로 등록합니다.|**블루프린트 인식 및 사용 기능 활성화**|C++에서 정의한 `FQuestData` 구조체를 블루프린트 함수에 파라미터로 넘겨주거나, 블루프린트 액터에 변수로 저장하여 퀘스트 상태를 관리할 때.|
|**`Blueprintable`**|(주의) `UCLASS`와 달리, `USTRUCT`에서는 이 지정자를 **사용하지 않습니다.**|-|-|

**결론적으로 `USTRUCT`를 블루프린트와 연동하고 싶다면 `USTRUCT(BlueprintType)`을 사용하는 것이 일반적입니다.**

---

### ■ 상황별 실전 예시 코드

#### 상황 1: 히트스캔 무기의 발사 결과 정보

- **설계**: 총알이 발사되었을 때, 맞은 위치, 맞은 표면의 재질, 성공 여부 등 여러 정보를 하나의 데이터 묶음으로 반환하고 싶습니다.
    

```cpp
// WeaponData.h

// 블루프린트에서 변수 타입으로 쓸 수 있도록 BlueprintType 지정
USTRUCT(BlueprintType)
struct FHitScanResult
{
    GENERATED_BODY()

    // FPROPERTY로 블루프린트에서 접근 가능하게 함
    UPROPERTY(BlueprintReadOnly)
    bool bDidHit;

    UPROPERTY(BlueprintReadOnly)
    FVector HitLocation;

    UPROPERTY(BlueprintReadOnly)
    TWeakObjectPtr<UPhysicalMaterial> HitMaterial;

    FHitScanResult() : bDidHit(false), HitLocation(FVector::ZeroVector), HitMaterial(nullptr) {}
};

// ...

UCLASS()
class ABaseWeapon : public AActor
{
    GENERATED_BODY()

    // 히트스캔 발사 결과를 FHitScanResult 구조체로 반환하는 함수
    UFUNCTION(BlueprintCallable, Category="Weapon")
    FHitScanResult FireHitScan();
};
```

- **해설**: `FireHitScan` 함수는 블루프린트에서 호출될 수 있으며, 그 반환 값인 `FHitScanResult` 핀에서 `Break` 노드를 사용하여 `bDidHit`, `HitLocation` 등의 내부 데이터에 쉽게 접근할 수 있습니다.
    

#### 상황 2: 몬스터의 능력치 설정

- **설계**: 몬스터 액터의 `Details` 패널에서 공격력, 방어력, 이동 속도 등의 능력치를 그룹화하여 깔끔하게 관리하고 싶습니다.
    

```cpp
// MonsterData.h

// 블루프린트에서 이 타입을 사용할 것이므로 BlueprintType 지정
USTRUCT(BlueprintType)
struct FMonsterStats
{
    GENERATED_BODY()

    // FPROPERTY 대신 UPROPERTY를 사용해도 USTRUCT 내부에서 동일하게 작동합니다.
    // FPROPERTY는 UHT 구버전과의 호환성을 위해 남아있으며, 현재는 UPROPERTY 사용이 권장됩니다.
    UPROPERTY(EditAnywhere, BlueprintReadOnly)
    float AttackPower;

    UPROPERTY(EditAnywhere, BlueprintReadOnly)
    float Defense;

    UPROPERTY(EditAnywhere, BlueprintReadOnly)
    float MovementSpeed;
};

// Monster.h
UCLASS()
class AMonster : public ACharacter
{
    GENERATED_BODY()

    // 몬스터의 능력치를 FMonsterStats 구조체 변수로 선언
    // EditDefaultsOnly: 이 몬스터의 '종류'(블루프린트)별로 기본 능력치를 설정.
    UPROPERTY(EditDefaultsOnly, Category="Monster Settings")
    FMonsterStats BaseStats;
};
```

- **해설**: `AMonster`의 블루프린트(`BP_Goblin`, `BP_Orc` 등)를 열고 `Class Defaults`를 보면 `Monster Settings` 카테고리 아래에 `Base Stats`가 있고, 이를 펼치면 `AttackPower`, `Defense`, `MovementSpeed`를 한 번에 편집할 수 있어 매우 편리합니다.