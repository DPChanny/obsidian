- **`UInputPopupWidget`**: 사용자에게 텍스트 입력을 받고, '확인' 버튼을 누르면 입력된 텍스트를 담아 델리게이트를 호출하는 Activatable Widget
    
- **`UMainMenuWidget`**: '이름 변경' 버튼을 누르면 위 팝업을 띄우고, 팝업이 전달한 텍스트 값을 받아서 화면에 표시하는 메인 위젯
    

---

### 1. 이름 입력 팝업 위젯 (UInputPopupWidget) C++ 코드

이 위젯은 데이터를 **보내는(Sender)** 역할을 합니다.

#### **InputPopupWidget.h**
```cpp
#pragma once

#include "CoreMinimal.h"
#include "CommonActivatableWidget.h"
#include "InputPopupWidget.generated.h"

class UCommonEditableText;
class UCommonButtonBase;

// 1. 델리게이트 시그니처(Signature) 선언
// FString 하나를 파라미터로 받는 동적 멀티캐스트 델리게이트를 정의합니다.
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnInputConfirm, const FString&, InputText);

UCLASS()
class YOURPROJECT_API UInputPopupWidget : public UCommonActivatableWidget
{
    GENERATED_BODY()

public:
    // 2. 델리게이트 인스턴스 선언
    // 외부에서 이 델리게이트에 함수를 바인딩(연결)할 수 있습니다.
    UPROPERTY(BlueprintAssignable, Category = "Events")
    FOnInputConfirm OnInputConfirm;

protected:
    virtual void NativeOnInitialized() override;

private:
    // UI 위젯 바인딩
    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UCommonEditableText> InputTextBox;

    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UCommonButtonBase> ConfirmButton;

    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UCommonButtonBase> CancelButton;
    
    // 버튼 클릭 핸들러
    UFUNCTION()
    void HandleConfirmClicked();

    UFUNCTION()
    void HandleCancelClicked();
};
```

#### **InputPopupWidget.cpp**
```cpp
#include "InputPopupWidget.h"
#include "Components/EditableText.h" // CommonEditableText 대신 기본 EditableText를 사용할 경우 필요
#include "CommonButtonBase.h"
#include "CommonEditableText.h"

void UInputPopupWidget::NativeOnInitialized()
{
    Super::NativeOnInitialized();
    
    if (ConfirmButton)
    {
        ConfirmButton->OnClicked().AddUObject(this, &UInputPopupWidget::HandleConfirmClicked);
    }
    if (CancelButton)
    {
        CancelButton->OnClicked().AddUObject(this, &UInputPopupWidget::HandleCancelClicked);
    }
}

void UInputPopupWidget::HandleConfirmClicked()
{
    if (InputTextBox)
    {
        // 입력된 텍스트를 가져옵니다.
        const FString EnteredText = InputTextBox->GetText().ToString();

        // 3. 델리게이트 브로드캐스트(Broadcast)
        // 델리게이트를 호출하여 바인딩된 모든 함수에 입력된 텍스트를 전달합니다.
        OnInputConfirm.Broadcast(EnteredText);
    }

    // 델리게이트 호출 후 위젯을 비활성화합니다.
    DeactivateWidget();
}

void UInputPopupWidget::HandleCancelClicked()
{
    // 취소 시에는 아무것도 하지 않고 위젯만 비활성화합니다.
    DeactivateWidget();
}
```

---

### 2. 메인 메뉴 위젯 (UMainMenuWidget) C++ 코드

이 위젯은 팝업을 띄우고 데이터를 **받는(Receiver)** 역할을 합니다.

#### **MainMenuWidget.h**
```cpp
#pragma once

#include "CoreMinimal.h"
#include "CommonUserWidget.h"
#include "MainMenuWidget.generated.h"

class UCommonButtonBase;
class UCommonActivatableWidgetStack;
class UTextBlock;
class UInputPopupWidget;

UCLASS()
class YOURPROJECT_API UMainMenuWidget : public UCommonUserWidget
{
    GENERATED_BODY()

protected:
    virtual void NativeOnInitialized() override;

private:
    // UI 위젯 바인딩
    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UCommonButtonBase> OpenInputPopupButton;

    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UCommonActivatableWidgetStack> PopupWidgetStack;

    UPROPERTY(meta = (BindWidget))
    TObjectPtr<UTextBlock> ResultText;

    // 에디터에서 팝업 위젯 블루프린트를 지정할 프로퍼티
    UPROPERTY(EditDefaultsOnly, Category = "UI")
    TSubclassOf<UInputPopupWidget> InputPopupWidgetClass;

    // 버튼 클릭 핸들러
    UFUNCTION()
    void HandleOpenPopupClicked();

    // 4. 콜백 함수 선언
    // 팝업의 델리게이트에 바인딩될 함수입니다. 시그니처가 반드시 일치해야 합니다.
    UFUNCTION()
    void HandlePopupInputConfirm(const FString& InputText);
};
```

#### **MainMenuWidget.cpp**
```cpp
#include "MainMenuWidget.h"
#include "CommonButtonBase.h"
#include "CommonActivatableWidgetStack.h"
#include "Components/TextBlock.h"
#include "InputPopupWidget.h"

void UMainMenuWidget::NativeOnInitialized()
{
    Super::NativeOnInitialized();

    if (OpenInputPopupButton)
    {
        OpenInputPopupButton->OnClicked().AddUObject(this, &UMainMenuWidget::HandleOpenPopupClicked);
    }
}

void UMainMenuWidget::HandleOpenPopupClicked()
{
    if (PopupWidgetStack && InputPopupWidgetClass)
    {
        // 팝업 위젯을 스택에 푸시하고, 생성된 인스턴스의 포인터를 받아옵니다.
        UInputPopupWidget* PopupInstance = PopupWidgetStack->PushWidgetClass<UInputPopupWidget>(InputPopupWidgetClass);
        
        if(PopupInstance)
        {
            // 5. 델리게이트에 콜백 함수 바인딩
            // 생성된 팝업 인스턴스의 델리게이트에 우리의 콜백 함수를 동적으로 연결합니다.
            PopupInstance->OnInputConfirm.AddDynamic(this, &UMainMenuWidget::HandlePopupInputConfirm);
        }
    }
}

void UMainMenuWidget::HandlePopupInputConfirm(const FString& InputText)
{
    // 6. 콜백 함수 실행
    // 팝업에서 OnInputConfirm.Broadcast()를 호출하면 이 함수가 실행됩니다.
    // 전달받은 InputText로 UI를 업데이트합니다.
    if (ResultText)
    {
        const FText DisplayText = FText::Format(FText::FromString(TEXT("입력된 이름: {0}")), FText::FromString(InputText));
        ResultText->SetText(DisplayText);
    }
}
```

---

### 실행 흐름 정리

1. `MainMenuWidget`에서 `OpenInputPopupButton`을 클릭하면 `HandleOpenPopupClicked` 함수가 호출됩니다.
    
2. `PopupWidgetStack->PushWidgetClass`를 통해 `InputPopupWidget`의 **새 인스턴스가 생성**되고 화면에 활성화됩니다.
    
3. 생성된 인스턴스의 `OnInputConfirm` 델리게이트에 `MainMenuWidget`의 `HandlePopupInputConfirm` 함수를 **`AddDynamic`으로 연결(바인딩)**합니다.
    
4. 사용자가 팝업의 텍스트 박스에 "MyPlayerName"을 입력하고 '확인' 버튼을 누릅니다.
    
5. `InputPopupWidget`의 `HandleConfirmClicked` 함수가 호출되어, 입력된 텍스트 "MyPlayerName"을 담아 `OnInputConfirm.Broadcast()`를 실행합니다.
    
6. `Broadcast`는 3단계에서 연결해 두었던 `MainMenuWidget`의 `HandlePopupInputConfirm` 함수를 **자동으로 호출**하면서 "MyPlayerName" 문자열을 인자로 전달합니다.
    
7. `MainMenuWidget`은 전달받은 텍스트로 `ResultText`를 업데이트하고, `InputPopupWidget`은 `DeactivateWidget()`을 호출하여 화면에서 사라집니다.
    

이러한 델리게이트 콜백 패턴은 각 위젯이 서로를 직접적으로 알 필요 없이 **느슨하게 결합(Loosely Coupled)**된 상태로 통신할 수 있게 해주므로, 재사용성이 높고 체계적인 UI 아키텍처를 만드는 데 매우 효과적입니다.