## FText 현지화(Localization) 작동 원리 및 조작법 (MD 버전)

FText의 현지화 원리는 **'키-값(Key-Value) 시스템'**임. 코드에는 텍스트 원문이 아닌 고유 **'키(Key)'**를 저장하고, 게임 실행 시 현재 언어 설정에 맞는 **'값(Value)'**, 즉 번역된 텍스트를 이 키를 통해 찾아와 표시하는 방식임.

---

### 1. FText 현지화 전체 흐름 (Workflow)

현지화 작업은 크게 **수집 → 번역 → 컴파일 → 실행**의 4단계로 구성됨.

#### **1단계: 텍스트 수집 (Gathering)**

- **무엇을?**: 코드와 애셋에 존재하는 모든 현지화 대상 `FText`를 찾아내는 작업임.
    
- **어떻게?**: 에디터 툴인 **'Localization Dashboard'**에서 'Gather Text' 기능을 실행함.
    
- **결과물**: 엔진이 `NSLOCTEXT` 등으로 표시된 텍스트를 모두 추출하여 `.po` 또는 `.archive` 파일로 생성함. 이 파일에는 텍스트의 키, 원문 등이 포함됨.
    

#### **2단계: 번역 (Translation)**

- **누가?**: 번역 담당자가 수행함.
    
- **어떻게?**: 1단계에서 생성된 `.po` 파일을 Poedit 같은 전문 번역 툴로 열어, 각 언어에 맞는 번역문을 입력함.
    
- **결과물**: 한국어용 `ko.po`, 일본어용 `ja.po` 등 언어별 번역 파일이 생성됨.
    

#### **3단계: 컴파일 (Compiling)**

- **무엇을?**: 번역된 `.po` 텍스트 파일들을 게임에서 빠르게 읽을 수 있는 바이너리 파일로 변환하는 과정임.
    
- **어떻게?**: **Localization Dashboard**에서 'Compile Text' 기능을 실행함.
    
- **결과물**: 각 언어의 `.po` 파일이 `.locres` (Localization Resource)라는 바이너리 파일로 컴파일됨. 이 파일이 최종 게임 패키지에 포함됨.
    

#### **4단계: 런타임 실행 (Runtime)**

- **언제?**: 플레이어가 게임을 실행할 때 작동함.
    
- **어떻게?**:
    
    1. 게임이 플레이어의 시스템 또는 인게임 설정에 따라 현재 문화권(예: "ko-KR")을 결정함.
        
    2. 엔진은 해당 문화권의 `.locres` 파일을 메모리에 로드함.
        
    3. 코드가 `FText` 표시를 요청하면, `FText`에 저장된 '키'를 이용해 로드된 `.locres` 파일에서 번역문을 찾아 화면에 출력함.
        
    4. 번역문이 없으면 매크로에 지정된 기본 텍스트(보통 영어)가 대신 출력됨.
        

---

### 2. FText 현지화 조작 방법

#### 가. 텍스트 표시 (매크로 사용)

코드에서 현지화 텍스트는 반드시 다음 매크로 중 하나로 감싸야 함.

- **`NSLOCTEXT("Namespace", "Key", "Default Text")`**
    
    - 가장 보편적으로 사용하는 매크로임.
        
    - **Namespace**: 텍스트의 카테고리. (예: "UI", "MainMenu"). 텍스트 관리를 용이하게 함.
        
    - **Key**: 번역문을 찾기 위한 고유 ID.
        
    - **Default Text**: 번역이 없을 때 표시될 기본 텍스트.
        
    ```cpp
    // 예시: 메인 메뉴의 환영 메시지
    FText WelcomeMessage = NSLOCTEXT("MainMenu", "WelcomeMessageKey", "Welcome to My Awesome Game!");
    ```
    
- **`LOCTEXT("Key", "Default Text")`**
    
    - 파일 상단에 `LOCTEXT_NAMESPACE`를 미리 정의하고 사용하는 단축형 매크로임.
        
    ```cpp
    #define LOCTEXT_NAMESPACE "MainMenu"
    
    FText PlayButtonText = LOCTEXT("PlayButtonKey", "Play");
    FText QuitButtonText = LOCTEXT("QuitButtonKey", "Quit");
    
    #undef LOCTEXT_NAMESPACE // 파일 끝에서 정의를 해제하는 것이 좋은 습관임.
    ```
    

#### 나. 동적 텍스트 포매팅 (`FText::Format`)

"점수: 100"처럼 변수가 포함된 텍스트는 `FString::Printf` 같은 단순 문자열 조합을 사용하면 안 됨. 언어별로 어순이 달라 번역이 깨지기 때문임. 이때 **`FText::Format`**을 사용해야 함.

- **방법**: `{Score}` 와 같은 포맷 지정자를 포함한 `FText` 패턴을 만들고, 런타임에 실제 값으로 교체함.
    
```cpp
// 1. 번역할 대상인 포맷 패턴 FText를 생성함.
// 영어: "Score: {Score}"
// 한국어: "점수: {Score}"
FText ScoreFormat = NSLOCTEXT("HUD", "ScoreFormat", "Score: {Score}");

// 2. 인자로 전달할 값을 FFormatNamedArguments 객체에 담음.
FFormatNamedArguments Args;
int32 PlayerScore = 12345;
Args.Add(TEXT("Score"), FText::AsNumber(PlayerScore)); // AsNumber는 숫자를 문화권에 맞게 변환함 (12,345)

// 3. FText::Format을 호출하여 최종 텍스트를 생성함.
FText FinalScoreText = FText::Format(ScoreFormat, Args);

// FinalScoreText는 현재 언어에 따라 "Score: 12,345" 또는 "점수: 12,345"가 됨.
```

이 방식은 번역가가 언어 문법에 맞게 `{Score}` 변수의 위치를 마음대로 조정할 수 있게 해주므로 어순 문제를 완벽히 해결함.

#### 다. Localization Dashboard

- **위치**: 에디터 메뉴 `Window` > `Localization Dashboard`
    
- **주요 기능**:
    
    - **`Gather Text`**: 프로젝트의 모든 현지화 텍스트를 수집함.
        
    - **`Compile Text`**: 번역된 텍스트를 바이너리 `.locres` 파일로 컴파일함.
        
    - **문화권(Culture) 관리**: 지원할 언어(ko, ja 등)를 추가하고 관리함.
        

---

### 3. 핵심 요약

- `FText` 현지화는 **'키'**를 기반으로 작동하며, 런타임에 현재 언어에 맞는 **'번역문'**으로 교체되는 시스템임.
    
- 개발자는 코드에 `NSLOCTEXT` 매크로 등으로 현지화 대상을 **'표시'**만 해두면 됨.
    
- **Localization Dashboard**는 텍스트 수집, 컴파일, 번역 관리를 위한 필수 툴임.
    
- 점수, 이름 등 동적 데이터는 반드시 **`FText::Format`**을 사용해 어순 문제를 해결해야 함.