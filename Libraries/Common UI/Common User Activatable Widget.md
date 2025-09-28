---
related: "[[Libraries/Common UI/Common User Widget|Common User Widget]]"
---


`UCommonActivatableWidget`은 `UCommonUserWidget`을 직접적으로 상속받는 하위 클래스입니다. 이 클래스는 활성화 및 비활성화가 가능한 UI, 특히 스택(Stack) 구조로 관리되는 UI 레이어를 구현하는 데 특화되어 있습니다.

---

### 핵심 역할 및 기능

#### 1. 활성화 및 비활성화 생명주기 관리 (Activation/Deactivation Lifecycle)

`UCommonActivatableWidget`의 가장 중요한 특징은 명확하게 정의된 활성화 생명주기를 갖는다는 것입니다. 위젯이 활성화되거나 비활성화될 때 특정 로직을 실행할 수 있도록 오버라이드 가능한 이벤트 함수를 제공합니다.

- **`OnActivated()`**: 위젯이 활성화될 때 호출됩니다. 주로 위젯이 표시될 때 시작되어야 하는 애니메이션 재생, 이벤트 바인딩, 내부 상태 초기화 등의 로직을 여기에 구현합니다.
    
- **`OnDeactivated()`**: 위젯이 비활성화될 때 호출됩니다. `OnActivated()`에서 수행했던 작업을 정리하는 로직(예: 이벤트 해제, 리소스 정리)을 배치합니다.
    

이러한 생명주기 관리를 통해 위젯의 상태를 예측 가능하고 체계적으로 제어할 수 있습니다.

#### 2. `UCommonActivatableWidgetStack`과의 연동

이 클래스는 `UCommonActivatableWidgetStack`이라는 컨테이너 위젯과 함께 사용하도록 설계되었습니다. `WidgetStack`은 `UCommonActivatableWidget`들을 레이어처럼 쌓고 관리하는 역할을 수행합니다.

- **위젯 푸시 (Push)**: `PushWidget()` 함수를 사용하여 스택의 최상단에 새로운 `UCommonActivatableWidget` 인스턴스를 추가하고 활성화시킵니다.
    
- **위젯 팝 (Pop)**: 스택의 최상단에 있는 위젯을 제거하고 비활성화시킵니다.
    
- **자동 상태 관리**: 스택에 위젯이 푸시되면 이전 최상단 위젯의 상태(예: `Visibility`)를 자동으로 관리해주는 옵션이 있습니다. 예를 들어, 새로운 위젯이 푸시될 때 이전 위젯을 숨기거나(Hidden), 비활성화(Collapsed) 상태로 만들 수 있습니다.
    

이를 통해 메뉴, 팝업, 설정창 등 사용자의 상호작용에 따라 동적으로 나타나고 사라지는 UI 플로우를 매우 효율적으로 구현할 수 있습니다.

#### 3. 입력 처리 및 모달리티(Modality) 제어

- **입력 캡처**: `UCommonActivatableWidget`이 활성화되면 Common UI 시스템은 해당 위젯에 입력 처리 우선권을 부여합니다. 이를 통해 스택 최상단의 활성화된 위젯만이 입력을 독점적으로 처리하는 모달(Modal) UI 동작을 쉽게 구현할 수 있습니다.
    
- **입력 액션 리스너**: 위젯이 활성화된 동안 특정 입력 액션(예: '뒤로 가기')을 감지하고 자동으로 위젯을 팝(pop)하는 등의 동작을 설정할 수 있습니다. `GetDesiredInputConfig` 함수를 오버라이드하여 위젯이 활성화될 때의 입력 모드(UI만, 게임 및 UI, 게임만)를 명시적으로 제어할 수 있습니다.
    

#### 4. 결과 반환 기능

`UCommonActivatableWidget`은 비활성화될 때 자신을 활성화시킨 호출자에게 결과를 반환하는 메커니즘을 제공합니다. `OnDeactivated` 이벤트에 델리게이트를 바인딩하여, 예를 들어 확인/취소 버튼이 있는 팝업 창이 닫힐 때 사용자가 어떤 선택을 했는지 상위 로직에 전달할 수 있습니다.

결론적으로, `UCommonActivatableWidget`은 단순히 보였다 사라지는 UI를 넘어, **상태, 입력, 데이터 흐름이 명확하게 관리되어야 하는 동적인 UI 레이어를 구축하기 위한 필수적인 클래스**입니다. Common UI 프레임워크의 강력한 워크플로우를 구성하는 핵심 요소라고 할 수 있습니다.

### 예제

```cpp
#pragma once

#include "CoreMinimal.h"
#include "CommonActivatableWidget.h"
#include "SettingsMenuWidget.generated.h"

class UCommonButtonBase;

UCLASS()
class YOURPROJECT_API USettingsMenuWidget : public UCommonActivatableWidget
{
    GENERATED_BODY()

protected:
    // UUserWidget의 생성자 역할을 하는 함수입니다. 위젯이 처음 생성될 때 호출됩니다.
    virtual void NativeOnInitialized() override;

    // 위젯이 활성화될 때 호출되는 함수입니다.
    virtual void OnActivated() override;
    
    // 위젯이 비활성화될 때 호출되는 함수입니다.
    virtual void OnDeactivated() override;

private:
    // UI 에디터에서 이 변수와 위젯을 바인딩해야 합니다.
    // 'CloseButton'이라는 이름의 UCommonButtonBase 위젯을 디자이너에서 추가하고 이 프로퍼티에 연결합니다.
    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UCommonButtonBase> CloseButton;

    // 닫기 버튼이 클릭되었을 때 호출될 함수입니다.
    UFUNCTION()
    void HandleCloseButtonClicked();
};
```

```cpp
#include "SettingsMenuWidget.h"
#include "CommonButtonBase.h"
#include "Kismet/KismetSystemLibrary.h" // 로그 출력을 위해 포함

void USettingsMenuWidget::NativeOnInitialized()
{
    Super::NativeOnInitialized();

    // 닫기 버튼이 유효한지 확인하고, OnClicked 이벤트에 함수를 바인딩합니다.
    if (CloseButton)
    {
        CloseButton->OnClicked().AddUObject(this, &USettingsMenuWidget::HandleCloseButtonClicked);
    }
}

void USettingsMenuWidget::OnActivated()
{
    Super::OnActivated();
    
    // 위젯이 활성화될 때 로그를 출력합니다.
    UKismetSystemLibrary::PrintString(this, TEXT("Settings Menu Activated."));
}

void USettingsMenuWidget::OnDeactivated()
{
    Super::OnDeactivated();

    // 위젯이 비활성화될 때 로그를 출력합니다.
    UKismetSystemLibrary::PrintString(this, TEXT("Settings Menu Deactivated."));
}

void USettingsMenuWidget::HandleCloseButtonClicked()
{
    // 이 위젯을 비활성화하고 스택에서 제거(Pop)하도록 요청합니다.
    DeactivateWidget();
}
```