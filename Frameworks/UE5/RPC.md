---
related:
  - "[[Frameworks/UE5/Authority|Authority]]"
---
UE5의 멀티플레이어 환경에서 RPC는 한 컴퓨터(클라이언트 또는 서버)가 네트워크로 연결된 다른 컴퓨터에서 함수를 실행하도록 요청하는 핵심 기능임. 이는 각 플레이어의 게임 경험을 동기화하는 데 필수적임.

---

## RPC의 종류와 동작 방식

RPC는 호출하는 주체와 코드가 실행되는 위치에 따라 세 가지로 명확히 구분됨.

### 1. Server RPC (클라이언트 → 서버)

클라이언트가 서버에게 특정 작업의 실행을 요청하는 통신 방식임.

* **호출 주체**: **클라이언트**. 단, 아무나 호출할 수 없으며 반드시 해당 액터의 **소유권(Ownership)**을 가진 클라이언트만 호출 권한을 가짐.
* **실행 위치**: **서버**. 클라이언트에서 호출된 함수지만, 함수의 실제 내용은 서버에서만 실행됨.
* **주요 용도**: 플레이어의 입력(공격, 아이템 사용 등)을 서버로 보내 유효성을 검증하고 게임 월드에 반영하기 위해 사용됨. 이는 모든 중요한 게임 로직을 서버가 통제하는 **서버 권위적(Server-Authoritative)** 구조의 핵심임.
```cpp
// PlayerCharacter.h
// 클라이언트가 서버에 발사를 요청하는 함수
UFUNCTION(Server, Reliable)
void Server_RequestFire();
```
2. Client RPC (서버 → 특정 클라이언트)
서버가 특정 클라이언트 한 명에게 함수를 실행하라고 명령하는 방식임.
 * 호출 주체: 서버.
 * 실행 위치: 해당 액터를 소유한 특정 클라이언트.
 * 주요 용도: 해당 플레이어에게만 영향을 주는 시각적/청각적 효과나 UI 업데이트에 사용됨. (예: '아이템 획득!' 메시지 표시, 피격 효과음 재생 등)
```cpp
// PlayerController.h
// 서버가 클라이언트에게 특정 위젯을 표시하라고 명령하는 함수
UFUNCTION(Client, Unreliable)
void Client_ShowNotificationWidget(const FString& Message);
```
3. Multicast RPC (서버 → 모두)
서버가 자신을 포함한 모든 클라이언트에게 함수를 실행하도록 전파하는 방식임.
 * 호출 주체: 서버. (만약 클라이언트가 호출하면, 다른 누구에게도 전파되지 않고 오직 해당 클라이언트 로컬에서만 실행됨)
 * 실행 위치: 서버와 모든 클라이언트.
 * 주요 용도: 게임 월드에서 일어나는 중요한 사건을 모든 플레이어가 동시에 봐야 할 때 사용됨. (예: 폭발 이펙트, 캐릭터 사망 애니메이션, 공용 문의 개폐 등)
```cpp
// ExplosiveBarrel.h
// 서버가 모든 클라이언트에게 폭발 이펙트를 재생하라고 전파하는 함수
UFUNCTION(NetMulticast, Unreliable)
void Multicast_PlayExplosionFX();
```
호출 권한 관리: 핵심은 '소유권'
RPC, 특히 Server RPC의 호출 권한은 **액터 소유권(Actor Ownership)**이라는 개념으로 관리됨.
 * 소유권이란?: 서버가 특정 액터를 특정 플레이어의 PlayerController에 할당하는 관계를 의미함. 이 관계가 성립되어야만 해당 클라이언트는 그 액터에 대해 Server RPC를 호출할 수 있음.
 * 기본적인 소유권 체인:
   * 플레이어가 서버에 접속하면 PlayerController가 생성되며, 이는 해당 클라이언트가 소유함.
   * 이 PlayerController가 캐릭터와 같은 Pawn에 **빙의(Possess)**하면, 해당 Pawn의 소유자 역시 이 PlayerController로 설정됨.
   * 따라서 클라이언트는 자연스럽게 자신의 PlayerController와 자신이 조종하는 Pawn에 대한 Server RPC 호출 권한을 갖게 됨.
Controller가 없다면? 대안은 '동적 소유권 설정'
PlayerController는 플레이어 입력을 처리하고 소유권의 시작점이 되므로 RPC 흐름의 중심에 있지만, 이것만이 유일한 방법은 아님.
 * 핵심 원리: RPC 권한은 Controller 클래스 자체에 있는 것이 아니라, 클라이언트의 연결(Connection)과 액터의 소유권 관계에 의해 결정됨.
 * 대안: 서버는 AActor::SetOwner(NewOwner) 함수를 사용해 어떤 액터의 소유자든 동적으로 변경할 수 있음.
   * 예시: 플레이어가 월드에 있는 '문' 액터와 상호작용할 때, 서버는 일시적으로 그 '문' 액터의 소유자를 플레이어의 PlayerController로 지정할 수 있음.
   * 이제 해당 클라이언트는 PlayerController나 Pawn이 아닌 '문' 액터를 대상으로 직접 Server RPC(예: Server_RequestOpenDoor)를 호출할 권한을 얻게 됨.
 * 결론: Controller가 없더라도, 서버가 특정 액터의 소유권을 클라이언트에게 부여한다면, 클라이언트는 해당 액터를 통해 서버와 통신할 수 있음.