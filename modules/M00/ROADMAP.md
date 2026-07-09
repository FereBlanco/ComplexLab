# M00 — Roadmap de configuración

Hoja de ruta para dejar ComplexLab operativo. Estructura pensada para volcarse a Jira tal cual:

- **Épica** = módulo M00 · **Historia** = feature M00.FYY · **Subtarea** = cada tarea de las tablas.

El identificador completo de cada tarea es `M00.FYY.TZZ`, donde `ZZ` es la columna `#` de su tabla a dos dígitos (p. ej. la fila 4 de M00.F01 es `M00.F01.T04`).

La columna **Claude** indica con qué opción (Chat/Cowork/Code) y modelo conviene hacer cada tarea; `Manual` = la haces tú fuera de Claude. Esta columna se copia a la descripción del ticket Jira.

Orden recomendado: F01 → F02 → F03 → F04 (F05 y F06 en paralelo cuando se quiera). Las features de desarrollo (M01+) no arrancan hasta cerrar F01–F04.

## M00.F01 — Conexión y estructura de Jira

Sin esto no hay seguimiento: es la primera porque el resto de features se gestionarán ya desde Jira.

| # | Tarea | Jira | Claude | Verificación |
|---|---|---|---|---|
| 1 | Autorizar el conector Atlassian en Claude (Ajustes → Conectores) | [CMX-8](https://fereblanco.atlassian.net/browse/CMX-8) | Manual | Claude lista los proyectos Jira ✔ hecho |
| 2 | Crear proyecto Jira `CMX` (ComplexLab, Kanban team-managed) en fereblanco.atlassian.net | [CMX-9](https://fereblanco.atlassian.net/browse/CMX-9) | Manual | Proyecto visible por API |
| 3 | Definir esquema: épica=módulo, historia=feature, subtarea=fase/tarea; estados alineados con el ciclo SDD | [CMX-10](https://fereblanco.atlassian.net/browse/CMX-10) | Cowork · Sonnet | Documentado en este archivo |
| 4 | Crear la épica M00 y sus historias/subtareas desde Claude | [CMX-11](https://fereblanco.atlassian.net/browse/CMX-11) | Cowork · Sonnet | Tickets visibles en el tablero |
| 5 | Anotar claves Jira en los README de módulos y en contratos | [CMX-12](https://fereblanco.atlassian.net/browse/CMX-12) | Cowork · Haiku | Campos rellenos |

## M00.F02 — Git y GitHub

| # | Tarea | Jira | Claude | Verificación |
|---|---|---|---|---|
| 1 | Crear repositorio remoto (GitHub) `complexlab` | [CMX-13](https://fereblanco.atlassian.net/browse/CMX-13) | Manual | Repo accesible |
| 2 | Inicializar Git en `repo/` y enlazar con el remoto | [CMX-14](https://fereblanco.atlassian.net/browse/CMX-14) | Code · Haiku | `git remote -v` correcto |
| 3 | Añadir `.gitignore` de Unity y `.gitattributes` con Git LFS para assets binarios | [CMX-15](https://fereblanco.atlassian.net/browse/CMX-15) | Code · Sonnet | Commit inicial limpio |
| 4 | Definir estrategia de ramas: `main` estable + `feature/MXX.FYY-short-name`, merge tras fase Testing | [CMX-16](https://fereblanco.atlassian.net/browse/CMX-16) | Cowork · Sonnet | Documentado en `repo/CLAUDE.md` |
| 5 | Crear `repo/CLAUDE.md` con convenciones de código (C#/Unity, commits, ramas) | [CMX-17](https://fereblanco.atlassian.net/browse/CMX-17) | Code · Sonnet | Archivo en el repo |
| 6 | Autorizar conector GitHub en Claude (opcional: PRs y revisiones desde Cowork) | [CMX-18](https://fereblanco.atlassian.net/browse/CMX-18) | Manual | Claude lee el repo |

## M00.F03 — Unity + VS Code + Claude Code

| # | Tarea | Jira | Claude | Verificación |
|---|---|---|---|---|
| 1 | Instalar Unity Hub + versión LTS (elegir y anotar versión exacta) | [CMX-19](https://fereblanco.atlassian.net/browse/CMX-19) | Manual | Editor abre |
| 2 | Crear proyecto Unity dentro de `repo/` (plantilla URP 2D/3D — decidir) | [CMX-20](https://fereblanco.atlassian.net/browse/CMX-20) | Manual | Proyecto compila |
| 3 | Configurar VS Code como editor externo de Unity (extensión C#, IntelliSense) | [CMX-21](https://fereblanco.atlassian.net/browse/CMX-21) | Manual | Autocompletado funciona |
| 4 | Instalar Unity Test Framework y crear un test de humo | [CMX-22](https://fereblanco.atlassian.net/browse/CMX-22) | Code · Sonnet | Test en verde |
| 5 | Probar Claude Code en `repo/`: pedirle un script trivial, compilar, borrar | [CMX-23](https://fereblanco.atlassian.net/browse/CMX-23) | Code · Sonnet | Ciclo Code→Unity verificado |
| 6 | Documentar el flujo de trabajo Cowork↔Code↔Unity en `documentation/TOOLS-GUIDE.md` | [CMX-24](https://fereblanco.atlassian.net/browse/CMX-24) | Cowork · Sonnet | Sección añadida |

## M00.F04 — Estándares técnicos y catálogo de patrones

| # | Tarea | Jira | Claude | Verificación |
|---|---|---|---|---|
| 1 | Definir convenciones C#: naming, carpetas de Assets/, namespaces, responsabilidades | [CMX-25](https://fereblanco.atlassian.net/browse/CMX-25) | Cowork · Sonnet | `documentation/STANDARDS.md` |
| 2 | Crear catálogo inicial de patrones Unity con criterio de uso: state machine, object pooling, observer/events, strategy, ScriptableObjects, separación simulación/render, ECS/Jobs (cuándo sí/no) | [CMX-26](https://fereblanco.atlassian.net/browse/CMX-26) | Cowork · Fable | `documentation/PATTERNS.md` |
| 3 | Definir la "Definition of Done" por fase del ciclo de vida | [CMX-27](https://fereblanco.atlassian.net/browse/CMX-27) | Cowork · Sonnet | Añadido a `PROJECT.md` |
| 4 | Definir estándar de pruebas: qué se testea siempre, cobertura mínima razonable, checklist manual tipo | [CMX-28](https://fereblanco.atlassian.net/browse/CMX-28) | Cowork · Sonnet | Añadido a `STANDARDS.md` |

## M00.F05 — Ecosistema Claude

| # | Tarea | Jira | Claude | Verificación |
|---|---|---|---|---|
| 1 | Crear skill `make-contract`: genera contrato desde plantilla preguntando lo necesario | [CMX-29](https://fereblanco.atlassian.net/browse/CMX-29) | Cowork · Sonnet | Skill funciona con M01.F01 |
| 2 | Decidir uso de subagentes: revisor de contratos, verificador de fases | [CMX-30](https://fereblanco.atlassian.net/browse/CMX-30) | Chat · Fable | Criterio en `TOOLS-GUIDE.md` |
| 3 | Explorar tareas programadas útiles (p. ej. resumen semanal del tablero Jira) | [CMX-31](https://fereblanco.atlassian.net/browse/CMX-31) | Cowork · Sonnet | Decisión tomada |
| 4 | Revisar conectores adicionales (Blender no tiene conector: flujo manual documentado) | [CMX-32](https://fereblanco.atlassian.net/browse/CMX-32) | Cowork · Haiku | Nota en `PROJECT.md` |

## M00.F06 — Estilo de publicaciones

| # | Tarea | Jira | Claude | Verificación |
|---|---|---|---|---|
| 1 | Definir audiencia, tono y estructura para blog y para LinkedIn (son formatos distintos) | [CMX-33](https://fereblanco.atlassian.net/browse/CMX-33) | Chat · Fable | `documentation/PUBLISHING.md` (redactado en Cowork) |
| 2 | Crear plantilla de post en `documentation/templates/` | [CMX-34](https://fereblanco.atlassian.net/browse/CMX-34) | Cowork · Sonnet | Plantilla creada |
| 3 | Definir checklist de publicación (capturas, GIF/vídeo, enlaces al repo) | [CMX-35](https://fereblanco.atlassian.net/browse/CMX-35) | Cowork · Haiku | En `PUBLISHING.md` |

## Criterio de cierre de M00

Ciclo completo ensayado en seco: ticket Jira → rama Git → cambio trivial en Unity → test → merge → ticket cerrado, todo orquestado desde Claude. Cuando esto funcione, M00 pasa a Closed y arranca M01.
