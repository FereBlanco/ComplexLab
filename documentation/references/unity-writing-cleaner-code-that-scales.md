# Unity — Writing Cleaner Code That Scales (E-Book, 2022)

> **Nota de proceso (ES):** transcripción de `2022_WritingCleanerCodeThatScales_EBook-v6-Final.pdf` (Unity Technologies, 59 páginas), guía oficial de Unity sobre C# style guides y clean code, subida por Fere para M00.F02.T05. Se ignoran capturas de pantalla del IDE (ya revisadas por Fere, sin aportar nada nuevo al texto). Contenido en inglés, tal como el original — igual criterio que las dos guías de control de versiones ya en esta carpeta.

Autor: Wilmer Lin, con contribuciones de Thomas Krogh-Jacobsen, Peter Andreasen, Scott Bilas y Robert LaCruise. © 2022 Unity Technologies.

## Introduction

Clean code is code that's easy to read and maintain — elegant, efficient, readable. It aims to make development scalable: consistent naming, legible formatting, small/readable classes and methods, and comments only where the code isn't self-explanatory. A cleaner codebase reduces the total cost of maintenance and eases onboarding of new developers.

## 1. Developing as a team

- **KISS (keep it simple, stupid):** find the simplest solution. Don't reinvent the wheel if Unity's Scripting API already solves it. The best code is no code at all.
- **YAGNI (you aren't gonna need it):** implement features only when you actually need them, not "just in case."
- **Don't code around the problem:** investigate the root cause instead of patching symptoms (e.g., a stray null-check that hides a deeper bug).
- **Improve incrementally, every day:** code cleanup is an ongoing, budgeted part of daily work — not a one-off event.
- **Make it good, not perfect:** don't refactor for its own sake; refactor when it provides a real benefit, then commit and move on.
- **Plan, but adapt:** software engineering is closer to gardening than architecture — expect to prune and adjust as the codebase grows.
- **Be consistent:** apply the same approach to similar problems, from naming to folder organization. Agree on a style guide as a team and follow it.
- **It takes a village:** clean code takes deliberate, shared effort — every team member reads and follows the style guide.
- A style guide is a subset of the Microsoft Framework Design Guidelines. It's a recommendation, not a hard rule — customize per team, and when it conflicts with this document, the team's guide wins (consistency over any specific rule).

## 2. Create a style guide

Microsoft C# Coding Conventions and Google's C# Style Guide are solid starting points. Unity also publishes an example C# style sheet.

### Naming conventions

- **Identifiers** must start with a letter or underscore; avoid special/Unicode characters (can break Unity command-line tooling).
- **Casing terminology:**
  - *Camel case* (`examplePlayerController`): local variables, method parameters.
  - *Pascal case* (`ExamplePlayerController`): classes, methods, public fields.
  - *Snake case* (`example_player_controller`): mentioned, not the Unity default.
  - *Kebab case* (`example-player-controller`): problematic in many languages (dash reads as minus/date separator).
  - *Hungarian notation* (`iCounter`, `strPlayerName`): old convention, not common in Unity dev.

- **Fields and variables:**
  - Use nouns for variables; prefix booleans with a verb (`isDead`, `hasDamageMultiplier`).
  - Use meaningful, searchable, pronounceable names — don't abbreviate except in math/loop counters.
  - Short "junk" names are fine for quick prototyping, refactor to meaningful names afterward.
  - Pascal case for public fields, camel case for private variables.
  - Optional underscore (`_`) prefix for private member variables to distinguish from locals — or rely on `this.` instead. Some guides also use `m_`, `k_` (const), `s_` (static) prefixes; agree as a team, since not all IDEs highlight scope equally well.
  - Be consistent about specifying (or omitting) access modifiers — omitted defaults to `private`.
  - One variable declaration per line.
  - Avoid redundant names (`Player.PlayerScore` → `Player.Score`) and avoid jokes/puns in names.
  - Use `var` when it improves readability and the type is unambiguous (e.g., long generic type names); avoid it when it obscures the type.

- **Enums:** pascal case, singular noun for the enum name (`WeaponType`), except `[Flags]` bitwise enums, which are typically plural (`AttackModes`) since they represent more than one value combined.

- **Classes and interfaces:** pascal case nouns. If a file contains a `MonoBehaviour`, the file name must match the class name (only one `MonoBehaviour` per file, though other internal classes are allowed). Prefix interfaces with `I` + adjective (`IKillable`, `IDamageable<T>`).

- **Methods:** start with a verb (`GetDirection`, `FindTarget`); camel case for parameters; methods returning `bool` should read as questions (`IsGameOver`, `HasStartedTurn`).

- **Events and event handlers:** name the event with a verb phrase, present/past participle for before/after (`OpeningDoor` / `DoorOpened`). Prefer `System.Action`/`Action<T>` delegates over custom `EventHandler` unless you need `EventArgs`. Prefix the raising method with `On` (`OnDoorOpened`). Prefix the observer's handling method with the subject's name + underscore (`GameEvents_DoorOpened`). Create custom `EventArgs` only when you actually need to pass extra data.

- **Namespaces:** pascal case, no symbols/underscores; use `using` to avoid repeating the prefix; use dot-delimited sub-namespaces to organize logical areas (`MyApplication.GameFlow`, `MyApplication.AI`, `MyApplication.UI`).

## 3. Formatting

- **Properties:** use expression-bodied syntax (`public int MaxHealth => maxHealth;`) for single-line read-only properties over a backing field; otherwise use the `{ get; set; }` syntax, with a private setter when write access shouldn't be public.

- **Serialization:** serialized fields must be `public` or tagged `[SerializeField]` (private/protected fields with the attribute still show in the Inspector while staying encapsulated). Use `[Range(min, max)]` to constrain numeric fields and render them as sliders. Group related data into a `[Serializable]` class/struct to keep the Inspector tidy.

- **Brace/indentation style:** Allman/BSD style (opening brace on its own new line) is what this guide uses, following Microsoft's Framework Design Guidelines; K&R (brace on the same line) is the common alternative. Pick one as a team and apply it uniformly. Decide a uniform indentation width (2 or 4 spaces) and don't omit braces even for single-line or nested statements — it keeps future edits (adding a line, a breakpoint) trivial and unambiguous. Standardize switch-statement indentation too.

- **EditorConfig:** a `.editorconfig` file defines a shared code style across IDEs (Visual Studio, Rider, etc. support it natively), travels with the repo, and works well with version control — takes precedence over personal/global editor settings.

- **Horizontal spacing:** add spaces to reduce density (`for (int i = 0; i < 100; i++)`, not `for(inti=0;i<100;i++)`); single space after commas between arguments; no space between a function name and its parentheses, nor just inside parentheses/brackets; single space around comparison operators and before flow-control conditions. Keep lines to a standard width (80–120 chars). Avoid column alignment unless it genuinely helps readability (e.g., bitwise tables) — it's maintenance overhead otherwise.

- **Vertical spacing:** group dependent/similar methods together; use blank lines sparingly to separate distinct sections (variable declarations vs. methods, classes vs. interfaces, if/else blocks when it helps).

- **Regions (`#region`):** if you're keeping classes small as recommended, you generally shouldn't need regions — treat them as a smell/anti-pattern signaling a class that should be split, not hidden. Decide as a team.

- Visual Studio / VS for Mac can enforce most of this automatically (Tools > Options > Text Editor > C# in Windows; Preferences > Source Code in Mac) and format-on-demand (`Ctrl+K, Ctrl+D` on Windows). Settings can be exported/shared, though an `.editorconfig` is the more portable, VCS-friendly option.

## 4. Classes

- Robert C. Martin's *Clean Code*: classes should be small — "the second rule is they should be even smaller than that."
- **The newspaper metaphor:** structure a class top-down like a news article — high-level/summary methods first, implementation detail later. A method like `ThrowBall` should appear before the `SetInitialVelocity`/`CalculateTrajectory` helpers it calls.
- **Class organization** (grouped, in this order): Fields → Properties → Events/Delegates → MonoBehaviour methods (`Awake`, `Start`, `OnEnable`, `OnDisable`, `OnDestroy`, ...) → Public methods → Private methods.
- **Single-responsibility principle:** each class does one thing. Worked example: a `Paddle` class that stores speed data, reads input, moves itself, and plays audio should instead be split into `PaddleData`/`ScriptableObject`, `PaddleInput`, `PaddleMovement`, `PaddleAudio`, with a `PaddleLogic` (or a thin `Paddle`) tying them together. Note: splitting responsibilities can *increase* total line count, but each piece becomes individually easier to read, debug, and extend.

## 5. Methods

- Small, single-responsibility, name reflects exactly what it does (`GetDistanceToTarget`).
- Use fewer arguments; avoid excessive overloading (and when you do overload, keep each signature's argument count distinct).
- Avoid side effects — a method should only do what its name promises; prefer passing by value over `ref`/`out` unless that's explicitly the method's one job.
- Don't use a boolean flag to switch behavior inside one method (`GetAngle(bool inRadians)`) — split into two clearly named methods (`GetAngleInDegrees`, `GetAngleInRadians`) instead.
- **Extension methods:** `static` method + `this` keyword on the first parameter to extend sealed/external types (e.g., extending `Transform` with `ResetTransformation`). Group them in dedicated static classes (`TransformExtensions`, `Vector3Extensions`, ...).
- **DRY (don't repeat yourself):** duplicate *logic* (not necessarily duplicate code) is the real problem — worked example refactors two near-identical `PlayExplosionA`/`PlayExplosionB` methods into one parametrized `PlayFXWithSound(ParticleSystem, AudioClip, Vector3)`.

## 6. Comments

- Most code shouldn't need comments if it follows KISS and is broken into small, well-named pieces — the code should explain "what"; comments should fill in "why" (non-obvious decisions, tricky logic), not restate the obvious.
- Don't use a comment to patch confusing code — restructure the code instead.
- Prefer a `[Tooltip("...")]` attribute over a comment for serialized fields (does double duty: shows in the Inspector too).
- Use `///` XML summary comments on public methods for IntelliSense support.
- One space after `//`; place comments on their own line rather than trailing code, in most cases.
- Style comments consistently (e.g., capitalized start, period at the end) — team's call, but pick one.
- No decorative asterisk/border blocks around comments.
- Remove commented-out code — rely on version control history instead of leaving dead code around.
- Keep TODOs current (add a name/date for accountability) and delete stale ones instead of letting them rot for years.
- Avoid "dev diary" journal comments and attribution comments (`// added by devA`) — that's what version control history is for.

## 7. Common pitfalls (code smells)

Enigmatic naming, needless complexity (God objects, oversized methods/classes), inflexibility (a small change forces many unrelated changes — usually a single-responsibility violation), fragility (a minor change breaks unrelated things), immobility (too many dependencies to reuse elsewhere), duplicate code, and excessive commentary (a symptom that the code itself isn't communicating well enough).

## 8. Conclusion / references

Clean code is a set of habits, not a fixed rulebook — the guide's own example C# style sheet is offered as a starting point to adapt, not a template to copy verbatim. Cited references: *Clean Code: A Handbook of Agile Software Craftsmanship* (Robert C. Martin, 2008) and *The Pragmatic Programmer, 20th Anniversary Edition* (David Thomas & Andrew Hunt, 2019).

## Appendix: Script templates

Unity ships default script templates under `ScriptTemplates` (`.../Editor/Data/Resources/ScriptTemplates` on Windows, inside the app package on Mac) using placeholders `#SCRIPTNAME#` and `#NOTRIM#`. Filename pattern: `PriorityNumber-MenuPath-DefaultName.FileExtension.txt` (double underscore `__` in `MenuPath` creates Create-menu submenus). Templates can be customized per-project by copying the `ScriptTemplates` folder under `Assets/` — safer than editing the global application resources, which affects every project using that Unity version.

## Appendix: Testing and debugging

- **TDD (test-driven development):** write a failing test → write the minimal code to pass it → confirm the whole suite still passes → refactor against the style guide → commit → repeat. Encourages small, frequent, revertible commits and continuous refactoring as a built-in step, not an afterthought.
- **Unity Test Framework (UTF, ex Unity Test Runner):** built on NUnit; supports Edit Mode and Play Mode tests, plus target-platform runs. Workflow: create a Test Assembly → create a C# test script (`Assets > Create > Testing > C# Test Script`) → run via the Test Runner UI.

---

## Notas de contraste con ComplexLab (proceso, no parte del original)

**Puntos que refuerzan lo que ya tenemos configurado:**

- El principio DRY, comentarios que expliquen el "por qué" y no el "qué", y borrar código comentado en vez de dejarlo — encajan directamente con la regla de higiene de commits ya añadida a `code/CLAUDE.md` (revisar el diff antes de comitear evita justo este tipo de ruido).
- La costumbre de comentarios de "diario" o atribución (`// added by devA`) que la guía desaconseja porque "para eso está el control de versiones" es coherente con nuestra filosofía de Conventional Commits: el historial de Git, no el código, es el lugar para ese contexto.
- TDD como flujo de commits pequeños y frecuentes con posibilidad de volver atrás encaja con la decisión ya tomada de no borrar ramas y conservarlas como material de práctica.

**Puntos que habrá que decidir juntos al concretar T05 (no hay decisión tomada todavía):**

- Casing y convenciones de nombres (pascal/camel case, prefijo `_` o `this.`, prefijos `m_`/`k_`/`s_`) — la guía deja explícitamente varias opciones abiertas; hace falta elegir una combinación concreta para `code/CLAUDE.md`.
- Allman vs. K&R para llaves, ancho de línea (80–120), 2 vs. 4 espacios de indentación — puramente una preferencia de equipo, sin recomendación única.
- Si usamos o no un `.editorconfig` versionado en `code/` para que VS Code aplique el estilo automáticamente (la guía lo recomienda especialmente para equipos con distintos IDEs; en solitario es opcional pero barato de tener).
- Organización de clases (Fields → Properties → Events → métodos de MonoBehaviour → públicos → privados) y la regla de "un MonoBehaviour por archivo" — encajan de forma natural en `code/CLAUDE.md` cuando exista el primer script real que las use de ejemplo.
- Unity Test Framework / TDD no estaba contemplado explícitamente en el contrato de M00.F02 ni en el roadmap; si se adopta, probablemente sea una decisión de M00.F04 (estándares técnicos) más que de T05 en sí.

**Sin relación con ComplexLab (ignorado, ni se transcribe en detalle):** las secciones sobre Accelerate Solutions (servicio de consultoría de pago de Unity) y las capturas de pantalla de configuración de Visual Studio/Rider — no aportan contenido normativo más allá del texto ya recogido arriba.
