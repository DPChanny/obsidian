---
type: framework
---

### 1. C++ 클래스 명명 규칙

C++ 코드 레벨에서의 명명 규칙은 클래스의 계승 관계나 타입을 명확히 하기 위해 사용됩니다. 주로 파스칼 케이스(PascalCase)를 따르며, 특정 타입의 클래스에는 정해진 접두사가 붙습니다.

|접두사|설명|예시|
|---|---|---|
|**`F`**|**구조체 (Struct) 또는 일반 클래스:** `UObject` 계열에 속하지 않는 대부분의 일반적인 C++ 클래스나 구조체에 사용됩니다. `F`는 'Float'나 'Framework'에서 유래했다는 설이 있지만, 현재는 가장 범용적인 접두사로 통용됩니다.|`FVector`, `FRotator`, `FHitResult`, `FMyCustomStruct`|
|**`T`**|**템플릿 (Template):** 템플릿으로 선언된 클래스나 구조체에 사용됩니다.|`TArray<T>`, `TMap<K, V>`, `TSubclassOf<T>`|
|**`U`**|**UObject 파생 클래스:** 언리얼 오브젝트(`UObject`)를 상속받는 모든 클래스에 사용됩니다. `UObject`는 언리얼 엔진의 핵심 클래스로, 리플렉션, 가비지 컬렉션, 직렬화 등의 기능을 제공합니다.|`UStaticMeshComponent`, `UMaterial`, `UGameInstance`|
|**`A`**|**액터 (Actor) 파생 클래스:** `AActor`를 상속받는 모든 클래스에 사용됩니다. 액터는 레벨에 배치할 수 있는 모든 오브젝트의 기본 단위입니다. `AActor` 역시 `UObject`를 상속받지만, 레벨에서의 구체적인 존재라는 특성 때문에 별도의 접두사 `A`를 사용합니다.|`APlayerController`, `ACharacter`, `AStaticMeshActor`|
|**`S`**|**슬레이트 UI 위젯 (Slate UI Widget):** 언리얼 엔진의 UI 프레임워크인 슬레이트(Slate)의 위젯 클래스에 사용됩니다. UMG 이전에 주로 사용되었던 C++ 기반의 UI 시스템입니다.|`SButton`, `STextBlock`, `SVerticalBox`|
|**`I`**|**인터페이스 (Interface):** 추상 인터페이스 클래스에 사용됩니다. 순수 가상 함수를 통해 클래스 간의 계약을 정의합니다.|`IInteractable`, `IDamageable`|
|**`E`**|**열거형 (Enum):** 열거형 타입에 사용됩니다.|`ECollisionChannel`, `EInputEvent`|
|**`b`**|**불리언 (boolean):** `bool` 타입 변수 앞에 붙여, 이름만으로도 참/거짓 값을 가지는 변수임을 명확히 합니다. (예: `bIsAlive`, `bCanJump`)|`bIsJumping`, `bHasWeapon`|

### 2. 콘텐츠 브라우저 에셋 명명 규칙

콘텐츠 브라우저에서 사용하는 에셋은 C++ 클래스와는 별개의 명명 규칙을 따릅니다. 일반적으로 **`에셋타입접두사_에셋이름_부가설명_번호`** 와 같은 형식을 사용하며, 이를 통해 에셋의 종류를 직관적으로 알 수 있습니다.

|접두사|에셋 타입|설명|예시|
|---|---|---|---|
|**`BP_`**|**블루프린트 (Blueprint)**|가장 일반적인 블루프린트 클래스입니다. 액터, 액터 컴포넌트, 오브젝트 등 다양한 기반 클래스로부터 생성될 수 있습니다.|`BP_PlayerCharacter`, `BP_Item_HealthPotion`|
|**`WBP_`**|**위젯 블루프린트 (Widget Blueprint)**|UMG(Unreal Motion Graphics) UI 에디터로 생성된 위젯 블루프린트입니다. 게임의 HUD나 메뉴 등의 UI를 만드는 데 사용됩니다.|`WBP_MainMenu`, `WBP_HUD_PlayerStatus`|
|**`T_`**|**텍스처 (Texture)**|이미지 파일로부터 임포트된 텍스처 에셋입니다. 이름 뒤에 `_D` (Diffuse/BaseColor), `_N` (Normal), `_M` (Mask) 등의 접미사를 붙여 용도를 구분하기도 합니다.|`T_Rock_D`, `T_Character_Body_N`|
|**`M_`**|**머티리얼 (Material)**|오브젝트의 표면을 렌더링하는 방법을 정의하는 머티리얼 에셋입니다.|`M_Metal_Chrome`, `M_Character_Skin`|
|**`MI_`**|**머티리얼 인스턴스 (Material Instance)**|부모 머티리얼의 파라미터를 변경하여 사용하는 머티리얼 인스턴스입니다. 성능 최적화에 유리합니다.|`MI_Metal_Gold`, `MI_Character_Skin_VariantA`|
|**`SM_`**|**스태틱 메시 (Static Mesh)**|움직이지 않는 3D 모델 에셋입니다. 건축물, 배경 소품 등에 사용됩니다.|`SM_Building_A`, `SM_Props_Chair`|
|**`SK_`**|**스켈레탈 메시 (Skeletal Mesh)**|스켈레톤(뼈대)을 가지고 있어 애니메이션이 가능한 3D 모델 에셋입니다. 캐릭터, 몬스터, 움직이는 기계 등에 사용됩니다.|`SK_Player_Female`, `SK_Monster_Dragon`|
|**`SKEL_`**|**스켈레톤 (Skeleton)**|스켈레탈 메시가 공유하는 뼈대 구조 에셋입니다.|`SKEL_Humanoid`, `SKEL_Quadruped`|
|**`ABP_`**|**애니메이션 블루프린트 (Animation Blueprint)**|스켈레탈 메시의 애니메이션 로직을 처리하는 블루프린트입니다.|`ABP_PlayerCharacter`, `ABP_Dragon`|
|**`AM_`**|**애니메이션 몽타주 (Animation Montage)**|여러 애니메이션 시퀀스를 조합하여 하나의 긴 애니메이션처럼 재생하거나, 특정 구간을 반복/재생할 수 있도록 하는 에셋입니다.|`AM_Attack_Combo`, `AM_Dodge_Roll`|
|**`AS_`**|**애니메이션 시퀀스 (Animation Sequence)**|단일 애니메이션 클립 에셋입니다.|`AS_Walk_Fwd`, `AS_Jump_Start`|
|**`BS_`**|**블렌드 스페이스 (Blend Space)**|두 개 이상의 애니메이션을 특정 값에 따라 부드럽게 혼합하는 에셋입니다. 주로 이동 애니메이션에 사용됩니다.|`BS_Player_Locomotion`|
|**`DT_`**|**데이터 테이블 (Data Table)**|구조체(Struct) 형태의 데이터를 표 형식으로 관리하는 에셋입니다. 게임 밸런싱 데이터나 아이템 정보 등을 저장하는 데 유용합니다.|`DT_ItemDatabase`, `DT_CharacterStats`|
|**`E_`**|**열거형 (Enum)**|블루프린트에서 사용할 수 있는 열거형 에셋입니다.|`E_WeaponType`, `E_CharacterState`|
|**`S_` 또는 `F_`**|**구조체 (Structure)**|블루프린트에서 사용할 수 있는 데이터 구조체 에셋입니다. C++의 `F` 접두사와 유사한 역할을 합니다. (커뮤니티나 프로젝트에 따라 `S_` 또는 `F_`를 사용합니다.)|`S_ItemInfo`, `F_CharacterAttributes`|
|**`PS_` / `FXS_`**|**파티클 시스템 (Particle System) / 나이아가라 시스템 (Niagara System)**|폭발, 연기, 스파크 등의 이펙트를 만드는 데 사용되는 에셋입니다. (PS_는 캐스케이드, FXS_는 나이아가라 시스템에 주로 사용됩니다.)|`PS_Explosion`, `FXS_Rain`|
|**`SND_` / `SC_`**|**사운드 웨이브 (Sound Wave) / 사운드 큐 (Sound Cue)**|`SND_` 또는 `SW_`는 단일 오디오 파일, `SC_` 또는 `A_` (Ambient Sound Actor)는 여러 사운드를 조합하거나 변형하여 사용하는 에셋을 의미합니다.|`SND_Gunshot`, `SC_Footstep_Grass`|