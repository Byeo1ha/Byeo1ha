# Unity 프로그래밍 규칙

## 기본 규칙

- 별도로 명시되지 않는 한, Unity C#을 사용이다.
- 기본 프로젝트 구조와 코딩 스타일을 유지한다.
- 복잡하거나 지나치게 추상적인 코드보다는 단순하고 읽기 쉬운 코드 작성을 선호한다.
- 추가로 요청하지 않는 한, 낯선 프레임워크나 패키지, 패턴을 소개하거나 설치하지 않는다.

## Unity C# 스타일

- 필드는 기본적으로 `private`를 사용한다.
- Inspector에서 참조 연결이나 설정값 입력이 필요한 필드에만 `[SerializeField] private`를 사용한다.
- `FindObjectOfType`, `GameObject.Find`등은 되도록 사용하지 않는다. 필요하면 개발자에게 직접 요청할 것을 요구한다.
- 문자열 기반 객체 조회를 되도록 사용하지 않는다. 필요하면 개발자에게 직접 요청할 것을 요구한다.
- 각 Class는 하나의 명확한 책임을 따르도록 개발한다.
- 불필요한 주석은 피하되, 의도가 명확하지 않을 때는 주석을 추가한다.
- 기존 코드의 naming convention을 따른다.


## 아키텍처

개발자는 다음과 같은 아키텍처를 구현할 수 있다.

- interface
- Singleton
- 상속
- Object Pooling
- ScriptableObject
- Observer Pattern (event Action)
- Dependency Injection

위 아키텍처 이외에 새로운 아키텍처를 사용하게 된다면 반드시 어떤 아키텍처를 사용했고 설계했는지 소개하고 간략하게 설명해라. 추가적인 개발자의 질문이 들어올 수 있다.

## 사용 가능 스택

- R3
- UniTask
- DOTween
- VContainer
- NewInputSystem

각 스택은 사용 가능하지만 아직 입문한지 얼마 안됐음으로 고도의 설계 실력을 가지지는 않았다. 이 점을 참고해라.

## R3

- Subject<T>
- Observable<T>
- ReactiveProperty<T>
- Subscribe()
- AddTo()
- Where()

개발자는 위 주요 API만 사용해본 경험이 있다. 따라서 되도록이면 추가적인 복잡한 R3의 새로운 기술 사용은 삼가한다. R3의 추가적인 기술이 정말 필요하다면 어떤 API를 사용했고 설계했는지 소개하고 간략하게 설명해라. 추가적인 개발자의 질문이 들어올 수 있다.

## VContainer

- LifetimeScope
- RegisterComponent
- RegisterComponentInHierarchy
- Keyed

개발자는 위 주요 API만 사용해본 경험이 있다. 따라서 되도록이면 추가적인 복잡한 VContainer의 새로운 기술 사용은 삼가한다. VContainer의 추가적인 기술이 정말 필요하다면 어떤 API를 사용했고 설계했는지 소개하고 간략하게 설명해라. 추가적인 개발자의 질문이 들어올 수 있다.

## Singleton 규칙

- 아래 Singleton 템플릿은 Scene 전환 이후에도 유지되어야 하는 전역 Singleton에 사용한다.
- Scene 내부에서 존재해야 하는 Singleton에는 `DontDestroyOnLoad`를 사용하지 않는다.
- 새로운 Singleton을 만들기 전에 기존 DI 구조나 객체 참조로 해결할 수 있는지 확인한다.

```csharp
public static ClassName Instance { get; private set; }

private void Awake()
{
    if (Instance == null)
    {
        Instance = this;
        DontDestroyOnLoad(gameObject);
    }
    else
    {
        Destroy(gameObject);
    }
}
```

- 불필요한 Singleton 생성은 하지 않는다.

## Observer Pattern 규칙

- Observer Pattern을 구현하게 될 경우 아래의 기술 중 하나를 선택해서 구현할 수 있다.

```
- R3
- event Action
- event Action<T>
```

- 인자 값을 전달해주지 않는 발행자를 설계해야할 경우 `event Action`을 사용한다.
- 인자 값을 전달해주어야하는 경우 R3의 구현 방식을 선호하고, R3가 설치되어있지 않으면 `event Action<T>`으로 구현한다.
- `event Action` 또는 `event Action<T>`의 경우, 구독은 OnEnable에서 한다.
- `event Action` 또는 `event Action<T>`의 경우, 구독 취소는 OnDisable에서 한다.
- R3의 구현 방식일 경우, `AddTo()`를 사용하여 구독 시점에 객체 생명주기를 관리하도록 한다.
- Object Pooling으로 재사용되는 객체에서는 `AddTo()`만으로 OnDisable 시점의 구독 해제가 보장된다고 가정하지 않는다.
- Disable 상태에서 이벤트를 수신하면 안 되는 객체는 구독 생명주기를 별도로 검토한다.

## Object Pooling 규칙

- `UnityEngine.Pool`을 사용한다.

## ScriptableObject 규칙

- 몬스터의 스탯, 플레이어 스탯, 스킬 데이터 같은 정적인 게임 플레이 데이터는 ScriptableObject를 사용한다.
- 런타임 변경 상태를 위해 원본 ScriptableObject의 값을 직접 변경하지 않도록 한다.
- 런타임 상태는 런타임 클래스에 저장하거나 따로 복사한 뒤, 복사된 데이터를 사용해야한다.

## New Input System 규칙

- 개발자는 Player Input 컴포넌트를 부여한 뒤 Behaviour 타입을 `Invoke Unity Events`로 바꾸고, Events에서 `CallbackContext`를 사용한다.
- 따라서 NewInputSystem 관련해서 입력이 감지되었을 때 실행할 함수를 구현할 때는 다음과 같아야한다.

```csharp
public void FunctionName(InputAction.CallbackContext context)
{

}
```

## 워크플로우 규칙

- 큰 변화가 일어날 작업은 어떻게 변경할지 먼저 계획을 설명한다.
- 변경 사항은 작고 개발자가 검토 가능하도록 한다.
- 관련 없는 파일은 리팩토링하지 않는다.
- 현재 방식이 학생 포트폴리오나 프로토타입에 적합하다면, 과도하게 복잡하게 설계하지 않는다.
- 코드를 검토할 때는 `반드시 고쳐야 할` 문제와 `개선하면 좋은` 제안을 구분한다.
- 사용자가 요청한 기능을 구현하는 데 직접적으로 필요한 기존 파일은 별도 허가 없이 수정할 수 있다.
- 요청 범위를 넘어서는 리팩토링은 하지 않는다.

다음 변경은 구현 전에 개발자에게 설명하고 허가를 받는다.

- 새로운 패키지 설치 또는 제거
- 기존 패키지 버전 변경
- 프로젝트 전체 아키텍처 변경
- 기존 public API의 큰 변경
- 파일 또는 폴더의 이동/삭제/대규모 이름 변경
- Project Settings 변경
- asmdef 구조 변경
- Scene 또는 Prefab 구조를 크게 변경하는 작업

## 리뷰 출력 규칙

코드 변경을 마치면 다음을 보고해야한다.

- 어떤 파일들이 변경되었는지
- 어떤 행동을 했는지
- 왜 변경을 이루었는지
- 변경 사항이 테스트되었는지 아니면 단순히 검토만 되었는지

## Codex Sandbox 노트

이 작업 공간에서는 일반적인 Windows sandbox실행이 다음과 같은 문제로 실패할 수 있다.

```
windows sandbox: spawn setup refresh
```

이것은 프로젝트의 문제가 아니라 환경 문제이다. 실패하지 않고 정상적으로 실행된다면 성공했다는 보고는 생략한다.

## 기타

### 한국어로 말할 때는 자연스러운 한국어 문법을 유지할 것

- 비격식적인 말투와 공손한 말투를 섞지 않는다. (즉 반 공손한 말투는 금지)
- "너", "나" 같은 대명사에 "~요"나 "~합니다" 같은 공손한 어미를 섞어 쓰지 않는다.
- 공손한 표현을 사용할 때는 자연스러운 공손한 한국어로 일관되게 사용한다.
- 되도록이면 프로젝트마다 말투를 통일하기 위해 공손한 말투를 기본으로 사용한다.

나쁜 예:
- "너 말이 맞아요."
- "내가 보기엔 너 코드가 더 적절해요."

좋은 예:
- "말씀하신 방식이 더 적절해 보입니다."
- "제가 제안한 코드보다 현재 코드가 의도에 더 맞는 것 같습니다."
