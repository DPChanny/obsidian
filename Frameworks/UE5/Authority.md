---
type: framework
---
### 1. 클래스 수준: `AActor` 클래스

가장 직접적인 답변은 **`AActor` 클래스 수준에서 지원된다**는 것입니다.

`HasAuthority()`는 `AActor`의 멤버 함수입니다. 이는 즉, `AActor`를 상속받는 모든 클래스는 이 함수를 사용할 수 있다는 의미입니다. 우리가 흔히 사용하는 대부분의 게임플레이 클래스들이 여기에 포함됩니다.

- `APawn`
    
- `ACharacter`
    
- `AController` (및 `APlayerController`, `AAIController`)
    
- `AGameModeBase`
    
- `AGameStateBase`
    
- `APlayerState`
    
- 기타 월드에 스폰되는 모든 액터들
    

반대로, `AActor`를 상속받지 않는 클래스, 예를 들어 `UObject`, `UGameInstance`, `UWidget` 등에서는 `HasAuthority()` 함수를 직접 호출할 수 없습니다.

### 2. 개념 수준: "원본이냐, 복사본이냐"

`HasAuthority()`의 진짜 의미를 이해하는 것이 중요합니다. 이 함수는 "이 코드가 서버에서 실행 중인가?"라는 질문보다 더 근본적인 질문에 답합니다.

**"지금 이 코드를 실행하는 액터 인스턴스가 네트워크상에서 원본(Authoritative Copy)인가?"**

언리얼 엔진의 서버-클라이언트 모델에서, 월드에 스폰되어 네트워크로 복제(`bReplicates = true`)되는 모든 액터는 **오직 한 개의 '원본'**과 **여러 개의 '복사본(Proxy)'**을 가집니다.

- **원본 (Authority):** 액터의 상태(위치, 체력 등)를 결정할 최종 권한을 가집니다. **서버**에 있는 액터가 항상 원본입니다.
    
- **복사본 (Proxy):** 서버에 있는 원본의 상태를 그대로 복제하여 클라이언트의 화면에 보여주는 역할을 합니다.
    

`HasAuthority()`는 바로 이 '원본'인지를 체크하는 함수이며, `true`를 반환하면 원본이라는 의미입니다.

### 3. 클래스별 동작 방식

`HasAuthority()`는 모든 액터에서 호출할 수 있지만, 클래스의 특성에 따라 그 의미와 사용법이 약간씩 다릅니다.

|클래스 종류|서버에서 `HasAuthority()`|클라이언트에서 `HasAuthority()`|설명|
|---|---|---|---|
|**복제되는 일반 액터** (`ACharacter` 등)|**`true`**|**`false`**|가장 일반적인 경우. 서버의 액터는 원본, 클라이언트의 액터는 복사본입니다.|
|**`AGameModeBase`**|**`true`**|**(존재하지 않음)**|GameMode는 서버에만 존재하므로, 그 인스턴스에서는 항상 `true`입니다.|
|**`APlayerController`**|**`true`**|**`false`**|서버는 모든 플레이어의 컨트롤러를 가지며, 이들은 모두 원본입니다. 클라이언트는 자신의 컨트롤러 '복사본'만 가집니다.|
|**`UActorComponent`**|**(소유자 액터에 따라 다름)**|**(소유자 액터에 따라 다름)**|컴포넌트 자체에는 `HasAuthority()`가 없습니다. 대신 `GetOwner()->HasAuthority()`를 호출하여 자신을 소유한 액터의 권한을 따릅니다.|

#### `PlayerController`의 특별한 경우: `IsLocalController()`

`PlayerController`는 `HasAuthority()`와 함께 `IsLocalController()` (또는 `IsLocalPlayerController()`)라는 중요한 함수를 가집니다.

- `HasAuthority()`: 이 컨트롤러 인스턴스가 **서버에 있는 원본**인가? (서버: O, 클라: X)
    
- `IsLocalController()`: 이 컨트롤러가 **이 컴퓨터에서 직접 조종하는 플레이어**의 것인가? (서버: Listen 서버의 1P인 경우 O, 클라: O)
    

따라서 다음과 같이 구분해서 사용합니다.

- **서버에서만 실행**하고 싶은 로직: `if (HasAuthority()) { ... }`
    
- **실제 플레이어의 화면**에서만(UI, 입력 처리 등) 실행하고 싶은 로직: `if (IsLocalController()) { ... }`
    

### 요약

- **지원 수준:** **`AActor` 클래스**와 그 모든 자식 클래스.
    
- **핵심 개념:** 해당 액터 인스턴스가 네트워크상의 **'원본'**인지를 확인하는 기능.
    
- **실질적 의미:** 서버-클라이언트 환경에서 `HasAuthority()`가 `true`라는 것은, **"이 코드는 지금 서버에서 실행 중인 액터의 코드다"** 라는 의미와 거의 동일합니다.
    