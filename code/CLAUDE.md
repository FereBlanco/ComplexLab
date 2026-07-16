# code/ — Convenciones de código

Este archivo lo lee Claude Code automáticamente al trabajar dentro de `code/` (el proyecto Unity). Convenciones de C#/Unity y estructura de `Assets/` se añadirán en M00.F02.T05, cuando exista contenido real que convencionar. Esta primera sección cubre la estrategia de ramas (M00.F02.T04); la segunda, una regla de higiene de commits adelantada de T05.

## Estrategia de ramas

1. **Una rama por feature, no por tarea.** `feature/MXX.FYY-short-name`, creada desde `main` actualizado. Todas las tareas `MXX.FYY.TZZ` de esa feature commitean sobre la misma rama.
2. **Actualizar con `main` antes de mergear, sin rebase.** Traer los cambios de `main` a la rama de feature (`git merge main`, o resolver conflictos que señale el PR) antes de abrir/cerrar el PR.
3. **Merge siempre vía Pull Request en GitHub**, nunca push directo de una rama de feature a `main`. El PR se abre cuando la feature alcanza la fase Testing de su contrato.
4. **Tipo de merge: "Create a merge commit"** (equivalente a `git merge --no-ff`) — nunca squash ni rebase-merge.
5. **Las ramas no se borran tras el merge.** Quedan como material de consulta y práctica de Git; se borran deliberadamente si sobran, no por defecto.
6. **`main` sin protección todavía.** M00 sigue necesitando push directo para tareas de configuración. Se reconsidera al empezar M01.
7. **Excepción M00:** mientras dure el módulo de configuración, los commits de tareas de M00 van directos a `main` (sin rama de feature), documentado en `modules/M00/features/M00.F02/contract.md` sección 3. Desde M01 en adelante aplican las reglas 1-6 sin excepción.
8. **Mensajes de commit:** Conventional Commits con el ámbito de la feature, p. ej. `feat(M01.F01): add cell grid simulation`.

## Higiene de commits

9. **No usar "stage all" / `git commit -a` por defecto.** Revisar el diff o la lista de archivos modificados antes de cada commit, y añadir (`git add <archivo>` o marcar el checkbox en VS Code) solo lo que pertenece de verdad al cambio que se está commiteando. Excepción: el primer commit de un proyecto, cuando todo lo que existe es justo lo que se quiere commitear. *(Pendiente para M00.F03: añadir aquí el matiz específico de Unity — el Editor puede marcar escenas/prefabs/sprite atlases como modificados sin cambios intencionados; no se documenta la regla concreta hasta que exista proyecto Unity real sobre el que verificarla.)*

## Convenciones de código C#

Decididas en M00.F02.T05 a partir de la guía oficial de Unity *Writing Cleaner Code That Scales* (`documentation/references/unity-writing-cleaner-code-that-scales.md`). Formato reforzado automáticamente por `code/.editorconfig`; lo que sigue es la referencia legible.

### Nombres

10. **Pascal case** para clases, interfaces, métodos, campos públicos y namespaces. **Camel case** para variables locales, parámetros y campos privados.
11. **Campos privados con prefijo `_`** (`_maxHealth`), camel case tras el guion bajo.
12. **Interfaces con prefijo `I`** + adjetivo (`IKillable`, `IDamageable<T>`).
13. **Variables: sustantivos.** Booleanos con prefijo verbal (`isDead`, `hasDamageMultiplier`). Sin abreviaturas salvo en bucles/expresiones matemáticas.
14. **Métodos: empiezan por verbo** (`GetDirection`). Los que devuelven `bool` se formulan como pregunta (`IsGameOver`).
15. **Enums: sustantivo singular** en pascal case (`WeaponType`), salvo los marcados `[Flags]`, que van en plural (`AttackModes`).
16. **Eventos:** frase verbal en presente/pasado participio para antes/después (`OpeningDoor` / `DoorOpened`); método que lo lanza con prefijo `On` (`OnDoorOpened`).
17. **Un `MonoBehaviour` por archivo**, con el nombre de archivo igual al de la clase.

### Formato

18. **Llaves estilo Allman:** llave de apertura siempre en línea propia.
19. **Indentación con tabuladores** (ancho 4). Aplicado automáticamente por `.editorconfig`.
20. **Ancho de línea: 100 caracteres.**
21. **No omitir llaves**, ni en sentencias de una sola línea ni en bucles anidados.

### Organización de clases

22. **Orden de miembros dentro de una clase:** Fields → Properties → Events/Delegates → métodos de MonoBehaviour (`Awake`, `Start`, `OnEnable`...) → métodos públicos → métodos privados.
23. **Principio de responsabilidad única:** si una clase hace más de una cosa (datos + input + lógica + audio...), se separa en clases más pequeñas.

### Comentarios

24. **El comentario explica el "por qué", no el "qué".** Si el código necesita un comentario para explicarse, es candidato a reescribirse con nombres más claros.
25. **Nada de comentarios de diario o atribución** (`// added by Fere`) — para eso está el historial de Git, coherente con la regla de higiene de commits ya existente (regla 9).
26. **Borrar el código comentado**, no dejarlo — está en el historial si hace falta recuperarlo.

*(Pendiente para M00.F03: verificar estas reglas contra un script real de Unity y ajustar lo que no encaje en la práctica. Testing/TDD queda fuera de T05, ver M00.F04.)*

Detalle y porqués de la estrategia de ramas, en lenguaje llano: `modules/M00/features/M00.F02/estrategia-de-ramas.md`. Especificación técnica completa: `modules/M00/features/M00.F02/contract.md`, sección 5 (T04).
