# Global Coding Rules

## Default Context
- Unless specified otherwise, assume Unity C#.
- Preserve my existing project structure and coding style.
- Prefer simple, readable code over clever or overly abstract code.
- Do not introduce unfamiliar frameworks, packages, or patterns unless I explicitly ask.

## Unity C# Style
- Prefer `[SerializeField] private` fields for Inspector references.
- Avoid public fields unless there is a clear reason.
- Use public read-only properties when outside access is needed.
- Prefer `Awake` for component reference caching.
- Avoid `GameObject.Find`, `FindObjectOfType`, and string-based object lookup unless I explicitly allow it.
- Keep each class focused on one clear responsibility.
- Avoid unnecessary comments, but add comments when the intent is not obvious.

## Singleton Rule
- If a singleton is needed, use this style:

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
 - Do not use lowercase public static ClassName instance; for new singleton code.
 - Do not create a singleton unless it is actually needed.

## Event / Observer Rule
 - Prefer event Action or event Action<T>.
 - Do not use raw delegate unless I explicitly ask.
 - Subscribe in OnEnable.
 - Unsubscribe in OnDisable.
 - Invoke events with ?.Invoke().
 
## Pooling Rule
 - Prefer simple List-based pooling.
 - Pre-instantiate pooled objects in Awake.
  

Store pooled objects in private readonly List<GameObject> pool.
Return inactive objects through a Get...() method.
Return null when no inactive object is available, unless I ask for auto-expansion.
Do not use UnityEngine.Pool.ObjectPool<T> unless I explicitly ask.

Example:
```
private readonly List<GameObject> pool = new List<GameObject>();

private void Awake()
{
    for (int i = 0; i < maxCount; i++)
    {
        GameObject obj = Instantiate(prefab, transform);
        obj.SetActive(false);
        pool.Add(obj);
    }
}

public GameObject GetObject()
{
    for (int i = 0; i < pool.Count; i++)
    {
        if (!pool[i].activeSelf)
            return pool[i];
    }

    return null;
}
```

## ScriptableObject Rule
Use ScriptableObject for static gameplay data such as turret stats, monster stats, wave data, item data, and skill data.
Do not directly mutate original ScriptableObject asset values for runtime-changing state.
Runtime state should be stored in runtime classes or copied data.

## Workflow Rule
Before making large changes, explain the plan first.
Keep changes small and reviewable.
Do not refactor unrelated files.
Do not modify existing files unless I explicitly allow it.
If multiple approaches are possible, explain the tradeoff briefly.
If my current approach is acceptable for a student portfolio or prototype, do not over-engineer it.
When reviewing code, separate “must fix” issues from “nice to improve” suggestions.
 

## When speaking in Korean, maintain a consistent speech level and natural Korean grammar.

- Never mix informal and polite speech (no half-polite style / 반존대).
- Do not mix pronouns like "너", "나" with polite endings such as "~요" or "~습니다".
- If using polite speech, consistently use natural polite Korean (e.g., "저", omit second-person pronouns when possible, or use the user's name respectfully if needed).
- If using casual speech, remain consistently casual without polite endings.
- Avoid awkward translated phrasing and prefer natural Korean conversational wording.
- Prefer grammatically natural Korean over literal translation from English.

Bad example:
"너 말이 맞아요."
"내가 보기엔 너 코드가 더 적절해요."

Good example (polite):
"말씀하신 방식이 더 적절해 보입니다."
"제가 제안한 코드보다 현재 코드가 의도에 더 맞는 것 같습니다."

Good example (casual):
"네 말이 맞는 것 같아."
"내가 제안한 코드보다 지금 코드가 의도에 더 맞아 보여."

## Scope Control Rule

* Do not introduce new architecture, managers, services, helper classes, utility classes, interfaces, or abstract base classes unless they are necessary for the requested change.
* Prefer modifying the smallest possible area of code.
* Do not change public APIs, serialized field names, scene references, prefab references, or Inspector-assigned references unless explicitly requested.
* Do not rename existing classes, methods, fields, files, or folders unless explicitly requested.
* Do not move code between files unless it is required for the requested change.

## Familiar C# Rule

* Prefer basic and familiar C# syntax.
* Avoid LINQ, reflection, async/await, advanced generics, extension methods, expression-bodied-heavy style, and complex lambda chains unless explicitly requested.
* Prefer clear `if`, `for`, `foreach`, `List<T>`, `Dictionary<TKey, TValue>`, `Coroutine`, and simple methods.
* If a more advanced feature seems useful, explain why first and wait for approval.

## Unity Safety Rule

* Do not add third-party packages or Unity packages unless explicitly requested.
* Do not use Addressables, UniTask, DOTween, Zenject, ECS, new Input System, UnityEngine.Pool, or other external/advanced systems unless explicitly requested.
* Do not assume scene hierarchy names, object names, tags, layers, or Animator parameter names. Ask or inspect existing code before relying on them.
* Do not use string-based Animator parameters repeatedly; cache hashes when appropriate, but do not refactor existing Animator code unless requested.

## Review Output Rule

After making changes, always report:

1. Which files were changed.
2. What behavior changed.
3. Why the change was made.
4. Any risks or Inspector setup required.
5. Whether the change was tested or only reviewed.

## Codex Sandbox Note

In this workspace, normal Windows sandbox execution may fail with:

windows sandbox: spawn setup refresh

Treat this as an environment issue rather than a project issue.

If file contents are already available through the IDE workspace, repository index, or editor context, continue using those sources instead of repeatedly retrying sandbox reads.

Do not assume project corruption from this error alone.
