
# Project: GunRogue

Listen Server 기반 멀티플레이어 PVE 로그라이크 슈팅 게임 개발 프로젝트입니다.  
팀프로젝트로 진행했으며, 이 저장소는 개인 기여 내용을 부가 설명하기 위한 사본 저장소 입니다.

- 개발 기간: 2025.10.24 - 2026.01.08
- 역할: 팀장 및 리드 프로그래머

<img width="512" alt="image" src="https://github.com/user-attachments/assets/345f8977-d1a8-4912-9eeb-26e1d75628d6" />


## 주요 외부 링크

| 링크 | 설명 |
|:--|:--|
| [원본 저장소](https://github.com/Team-Fairy-pitta/ProjectGunRogue) | 실제로 프로젝트를 진행한 저장소입니다. 이 저장소에서 commit 내역과 PR 내역을 확인할 수 있습니다. |
| [플레이 영상]() | 실제 게임 플레이 영상을 확인할 수 있습니다. |
| [프로젝트 설명](https://15danpancake.atlassian.net/wiki/spaces/portfolio/pages/157089849/Project+GunRogue) | 프로젝트에 대한 설명, 문제 해결 과정, 구현 내용을 확인할 수 있는 기술 블로그입니다. |


## 본 문서의 목표

프로젝트에 대한 자세한 설명은 위의 '프로젝트 설명' 페이지에서 확인할 수 있으므로, 이 README에서는 다른 내용을 다룰 것입니다.  
빠르게 확인할 수 있도록 코드 일부를 이 문서에 작성할 것이며, 원하시면 직접 코드와 commit 내역을 확인할 수 있습니다.

- WHAT and HOW: 이 개발자가 **어떤** 코드를 **어떻게** 작성했는지
- PR 사례: 이 개발자가 어떻게 협업하는지

## 코드 작성: WHAT and HOW

### 방어적 프로그래밍

객체에 접근하기 전에 먼저 유효성을 검사해야 합니다.

`Source/GunRogue/Character/Interaction/GRInteractionAbility.cpp`

```cpp
void UGRInteractionAbility::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* OwnerInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthority(&ActivationInfo))
	{
		if (!CommitAbility(Handle, OwnerInfo, ActivationInfo))
		{
			EndAbility(Handle, OwnerInfo, ActivationInfo, true, false);
			return;
		}

		if (!OwnerInfo)
		{
			UE_LOG(LogTemp, Error, TEXT("OwnerInfo is INVALID"));
			EndAbility(Handle, OwnerInfo, ActivationInfo, true, false);
			return;
		}

		if (!OwnerInfo->AvatarActor.IsValid())
		{
			UE_LOG(LogTemp, Error, TEXT("OwnerInfo->AvatarActor is INVALID"));
			EndAbility(Handle, OwnerInfo, ActivationInfo, true, false);
			return;
		}

		// 후략
```

### 함수의 크기는 작게, 함수의 이름은 명확하게

- 함수가 너무 많은 일을 하면 유지 보수에 악영향을 줍니다. 함수는 하나의 역할만 할 수 있도록 작게 작성하려고 노력합니다.
- 함수의 이름을 명확하게 지으려고 노력합니다. 함수의 이름만 읽어도 어떤 역할을 하며, 어떤 값을 반환하는지 알 수 있게 하려고 노력합니다.


`Source/GunRogue/Item/GRItemActor.h`

```cpp
UCLASS()
class GUNROGUE_API AGRItemActor : public AActor, public IGRInteractableActor
{
	GENERATED_BODY()
	
public:
	AGRItemActor();
	virtual void BeginPlay() override;
	virtual bool IsNetRelevantFor(const AActor* RealViewer, const AActor* ViewTarget, const FVector& SrcLocation) const override;

	// IGRInteractableActor
	virtual void InteractWith(AActor* OtherActor) override;
	virtual void OnOver() override;
	virtual void OnOut() override;
	virtual bool CanInteract(AActor* OtherActor) override;

private:
	void PlaceActorOnGround();
	FVector GetGroundPointUsingLineTrace();
};
```

### 기타 코드의 유지 보수성을 높이기 위한 노력들
- 불필요한 주석은 작성하지 않음
    * 코드를 설명하는 가장 좋은 방법은 코드라고 생각합니다.
    * 코드만 읽어도 이해할 수 있도록 코드를 작성하려고 노력합니다.
    * 따라서, 저는 코드를 그대로 설명하는 주석을 작성하지 않습니다.
    * 코드만으로 충분히 설명할 수 없는 경우에만 주석을 최소한으로 작성합니다. (ex. 매직 넘버)
- 변수의 이름을 명확하게 작성
    * 지역 변수나 반복문 안에서 사용하는 변수의 이름도 신경써서 작성합니다.
- cpp 파일 분할
    * 큰 cpp 파일을 여러 개의 cpp으로 분할했습니다.
    * 코드 유지 보수성을 높이면서, 동시에 컴파일 시간을 줄일 수 있습니다.

## PR 리뷰 사례

더 많은 PR 사례는 원본 저장소의 [Pull Request](https://github.com/Team-Fairy-pitta/ProjectGunRogue/pulls?q=is%3Apr+is%3Aclosed) 페이지를 참고 바합니다.

### 꼼꼼한 코드 리뷰 사례

https://github.com/Team-Fairy-pitta/ProjectGunRogue/pull/72

<img width="799" height="470" alt="image" src="https://github.com/user-attachments/assets/d956382e-5b35-4655-b4aa-1990d69f26c5" />

<img width="807" height="523" alt="image" src="https://github.com/user-attachments/assets/dea9cc9e-e6a3-4546-8fd1-8d9ae765a03e" />

<img width="805" height="364" alt="image" src="https://github.com/user-attachments/assets/3b394a16-ba22-4096-ad04-bf68cf569a96" />

https://github.com/Team-Fairy-pitta/ProjectGunRogue/pull/162

<img width="808" height="303" alt="image" src="https://github.com/user-attachments/assets/58c5bf20-4ae4-4916-8280-11749cb93958" />

<img width="798" height="365" alt="image" src="https://github.com/user-attachments/assets/978ecd73-c2d9-4e65-ada0-2c1bc62a62f3" />


### PR 리뷰 및 테스트를 통해 버그의 원인을 찾은 사례

https://github.com/Team-Fairy-pitta/ProjectGunRogue/pull/162

<img width="845" height="374" alt="image" src="https://github.com/user-attachments/assets/a2337da0-6444-4adf-9785-2ccdc73772b8" />




## 프로젝트 소스 코드 구성 설명

```
Source                    
└─GunRogue                
    ├─AbilitySystem       GAS 관련 코드
    ├─AI                  AI Enemy 관련 코드
    ├─Augment             증강 관련 코드 (캐릭터 강화 요소 중 하나)
    ├─Character           캐릭터 관련 코드
    │  ├─Attachment       캐릭터의 부착물 (무기) 관련 코드
    │  └─Interaction      캐릭터와 물체의 상호 작용 관련 코드
    ├─EnemySpawn          적 Spawn 관련 코드
    ├─GameModes           GameMode 관련 코드
    ├─Goods               바닥에 드롭되는 Actor 관련 코드
    ├─Input               입력 제어 관련 코드
    ├─Item                아이템 관련 코드 (캐릭터 강화 요소 중 하나)
    ├─MetaProgression     성장 시스템 관련 코드 (로그라이트의 영구 성장 요소)
    ├─MiniMap             미니맵 관련 코드
    ├─Player              Player Controller, Player State 관련 코드
    │  ├─Battle               전투 맵에서의 PC, PS 관련 코드
    │  ├─GameStart            게임 시작 화면에서의 PC, PS 관련 코드
    │  └─Lobby                로비에서의 PC, PS 관련 코드
    ├─System              맵 로드와 같은 시스템 관련 코드
    ├─UI                  Widget 관련 코드
    └─Weapon              캐릭터가 사용하는 무기 관련 코드 (캐릭터 강화 요소 중 하나)

```

