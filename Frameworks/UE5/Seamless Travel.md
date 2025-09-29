### Seamless Travel 과정 요약

`PostSeamlessTravel` 호출 이후, GameMode는 이전 월드에 있던 각 플레이어에 대해 `HandleSeamlessTravelPlayer`를 순차적으로 호출합니다. 이 함수 안에서 새로운 `GameState`에 `AddPlayerState`가 호출되어 플레이어가 추가됩니다. `BeginPlay`는 월드 초기화 단계에서 호출되므로, 플레이어가 모두 이동하기 전이나 중간에 호출될 수 있습니다.

---

### NumPlayers와 NumTravelingPlayers 변화

- **`NumTravelingPlayers`**: Seamless Travel 시작 시 이전 월드의 플레이어 수로 초기화되며, `HandleSeamlessTravelPlayer`가 성공적으로 완료될 때마다 1씩 **감소**합니다.
    
- **`NumPlayers`**: 새로운 `GameState`에서 0으로 시작하며, `HandleSeamlessTravelPlayer` 내부에서 `AddPlayerState`가 호출될 때마다 1씩 **증가**합니다.
    

---

### InitSeamlessTravelPlayer 호출 순서

`AGameModeBase::HandleSeamlessTravelPlayer` 함수 내부에서, 이전된 `PlayerController`의 재초기화를 위해 `APlayerController::InitSeamlessTravelPlayer` 함수가 호출됩니다.

---

### InitGame과 PostLogin이 호출되지 않는 이유

- **`InitGame`**: 완전히 새로운 게임 세션을 시작할 때 호출됩니다. Seamless Travel은 기존 세션을 이어가는 것이므로 호출되지 않습니다.
    
- **`PostLogin`**: 클라이언트가 서버에 새로 접속했을 때 호출됩니다. Seamless Travel은 기존 `PlayerController`를 이전하는 방식이므로 '새로운 로그인'으로 취급하지 않아 호출되지 않습니다.
    

---

### PlayerState의 CopyProperties

새로운 월드에서 새 `PlayerState`가 생성된 후, `OverrideWith` 함수를 통해 기존 `PlayerState`의 데이터를 복사합니다. 이 함수 내부에서 `virtual` 함수인 `CopyProperties`가 호출되며, 개발자는 이 함수를 재정의하여 어떤 데이터를 이전할지 직접 제어할 수 있습니다. 기본적으로 `Replicated` 지정자가 붙은 대부분의 프로퍼티가 복사됩니다.