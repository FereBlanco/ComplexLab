# Dossier M00.F02 — Control de versiones: Git, GitHub y el repositorio de ComplexLab

| Campo | Valor |
|---|---|
| Feature | M00.F02 — Git y GitHub: repositorio, ramas, conector ([CMX-3](https://fereblanco.atlassian.net/browse/CMX-3)) |
| Fecha | 2026-07-08 |
| Estado del dossier | Inicial (se actualizará con conclusiones al cerrar la feature) |

## 1. Qué vamos a hacer

Convertir `repo/` en un repositorio Git enlazado a GitHub, preparado para un proyecto Unity: ignorando lo que no debe versionarse, gestionando los binarios con LFS, con una estrategia de ramas ligada a las features, y con las convenciones de código escritas en `repo/CLAUDE.md` para que Claude Code las respete siempre.

## 2. Los conceptos

### Control de versiones

Un sistema de control de versiones guarda la historia completa de un conjunto de archivos: cada estado por el que pasó, quién lo cambió, cuándo y por qué. Resuelve tres problemas que sin él se pagan caros: **poder volver atrás** cuando algo se rompe, **entender el pasado** (por qué el código es como es) y **trabajar en paralelo** sin pisarse. En un proyecto de aprendizaje hay un cuarto beneficio: la historia del repositorio es literalmente el diario de lo aprendido.

### Git

Git (creado por Linus Torvalds en 2005 para gestionar el código de Linux) es el sistema de control de versiones dominante. Sus piezas básicas:

| Concepto | Qué es |
|---|---|
| **Repositorio** | La carpeta versionada junto con toda su historia (guardada en un subdirectorio oculto `.git`) |
| **Commit** | Una fotografía del proyecto en un instante, con autor, fecha y mensaje. Es la unidad de historia: pequeña, atómica, con un mensaje que explica el porqué |
| **Rama** (branch) | Una línea de desarrollo paralela. Crear una rama es instantáneo y gratuito; permite experimentar sin tocar lo estable |
| **Merge** | Reincorporar los commits de una rama a otra. El momento en que el trabajo "aterriza" |
| **Remoto** (remote) | Una copia del repositorio en otro sitio (GitHub). `push` envía tus commits al remoto; `pull` trae los ajenos |
| **.gitignore** | Lista de patrones de archivos que Git debe ignorar: generados, temporales, dependencias |

Git es **distribuido**: tu copia local contiene toda la historia y funciona sin conexión. GitHub no es Git — es un servicio que aloja repositorios Git y añade lo social: issues, pull requests, revisiones, y las APIs que usan herramientas como el conector de Claude.

### Pull request (PR)

Una petición formal de merge en GitHub: "quiero incorporar esta rama a main; revisadla". En equipos es el punto de control de calidad. En solitario puede parecer teatro, pero tiene un uso real aquí: una PR es el lugar natural para que **Claude revise el código antes del merge** (la skill de code-review trabaja sobre PRs o diffs).

### Los problemas específicos de Unity

Unity genera un proyecto donde conviven tres tipos de archivo, y tratarlos igual arruina el repositorio:

1. **Código y escenas** (`Assets/`, `ProjectSettings/`, `Packages/`) — texto, se versiona siempre.
2. **Archivos regenerables** (`Library/`, `Temp/`, `obj/`, `Logs/`) — Unity los reconstruye desde cero; son gigabytes de ruido que jamás deben entrar en Git. De ahí el `.gitignore` específico de Unity.
3. **Binarios** (texturas, modelos 3D de Blender, audio) — Git guarda cada versión completa de un binario (no puede calcular diferencias útiles), así que un .png de 5 MB editado 20 veces son 100 MB de historia para siempre.

Para el punto 3 existe **Git LFS** (Large File Storage): en vez de guardar el binario en la historia, guarda un puntero de texto y deposita el archivo real en un almacén aparte, descargando solo la versión actual. Se activa por tipo de archivo en `.gitattributes`. GitHub da 1 GB gratuito de LFS — suficiente para empezar; si se llena, será un problema documentable.

Además, dos ajustes de Unity son obligatorios para que Git funcione bien: **assets serializados como texto** (Force Text, para poder ver diferencias en escenas y prefabs) y **archivos .meta visibles** (contienen los identificadores que Unity usa para enlazar assets; perderlos rompe referencias).

### Conventional Commits

Convención para mensajes de commit: `tipo(ámbito): descripción` — por ejemplo `feat(M01.F01): add cell grid simulation`, `fix: correct neighbor count at grid edges`, `docs: update module README`. Tipos habituales: `feat`, `fix`, `docs`, `test`, `refactor`, `chore`. El beneficio: la historia se lee como un registro estructurado, filtrable y hasta procesable por herramientas.

## 3. Por qué ComplexLab se organiza así

### La estrategia de ramas: trunk ligero por feature

```
main ──●───────●──────────●────→  (siempre estable, siempre compila)
        \     /            \
         ●──●  feature/M01.F01-game-of-life
```

Reglas: `main` siempre compila y pasa los tests; cada feature vive en `feature/MXX.FYY-short-name`; el merge a main solo ocurre cuando la feature alcanza la fase Testing de su contrato. Sin ramas permanentes de develop/release (GitFlow): esa maquinaria existe para coordinar equipos con versiones simultáneas, y aquí sería puro peso muerto. La razón de tener ramas siquiera — pudiendo committear a main directamente — es doble: ensayar el flujo profesional (objetivo de aprendizaje explícito) y que main sea siempre un refugio estable al que volver si un experimento sale mal.

**Corrección (2026-07-10):** las ramas *no* se borran automáticamente al mergear. Decisión de Fere: quiere conservarlas como material de práctica de Git (rebase, comparación de historiales, recuperación de una rama vieja, etc.) — el propio objetivo de aprender el flujo profesional pesa más aquí que la limpieza del repositorio. Si con el tiempo alguna rama sobra, se borra deliberadamente, no por defecto. Esto revierte lo que decía originalmente esta sección ("la rama se borra tras el merge"); queda como ejemplo de que un contrato/dossier se corrige cuando la práctica lo desmiente, no se parchea en silencio.

### El nombre de la rama lleva el ID de la feature

`feature/M01.F01-game-of-life` conecta tres mundos con una sola cadena: el contrato (`modules/M01/features/M01.F01/`), el ticket Jira (la historia de M01.F01) y el código. Los mensajes de commit añaden el ámbito (`feat(M01.F01): ...`) y así cualquier línea de código es rastreable hasta su porqué en segundos. La trazabilidad no es burocracia: es lo que permite, meses después, entender qué decisión produjo qué código.

### Por qué solo `repo/` está en Git (de momento)

Contratos, dossiers y documentación viven fuera del repositorio de código. Es una decisión discutible — muchos equipos versionan la documentación junto al código — pero aquí separa dos ritmos distintos: la documentación la gestiona Cowork con acceso directo a carpetas, y el código lo gestiona Claude Code con Git. Si la separación duele (por ejemplo, contratos desincronizados del código que describen), lo anotaremos en conclusiones y reconsideraremos: sería un aprendizaje de primera.

### Por qué `repo/CLAUDE.md`

Claude Code lee automáticamente el `CLAUDE.md` de la carpeta donde trabaja. Escribir ahí las convenciones (versión de Unity, naming C#, estructura de Assets/, formato de commits, estrategia de ramas) convierte las normas en comportamiento: no habrá que repetirlas en cada sesión, y cualquier sesión futura las respetará. Es el equivalente en código del `CLAUDE.md` raíz que ya gobierna la gestión del proyecto.

### Por qué el conector GitHub además de Git local

Con Git local, Claude Code hace todo el trabajo de versionado. El conector GitHub añade lo que vive en la web: leer y comentar PRs desde Cowork, revisar diffs sin abrir el terminal, y en el futuro automatizaciones (¿un resumen semanal de actividad del repo?). Es opcional y lo tratamos como tal — M00.F02.T06 — pero completa el triángulo Jira–GitHub–Claude.

## 4. Conclusiones

*(Pendiente el cierre completo de M00.F02 — T04-T06 siguen abiertas. Nota parcial registrada ahora porque ya ocurrió el aprendizaje que la sección 3 anticipaba.)*

**Sobre la separación código/documentación (2026-07-10):** la separación "discutible" planteada en la sección 3 (`repo/` en Git, `documentation/`/`dossiers/`/`modules/` fuera) sí dolió, y no hizo falta esperar a que doliera de verdad para verlo: bastó con pensarlo dos veces. Se reconsideró antes de que existiera proyecto Unity — ventana barata, sin binarios ni GUIDs de Unity que reubicar — y se aplanó `ComplexLab/` a un único repositorio Git, con el código en `code/` (subcarpeta con su propio `.gitignore` anclado) y el resto de carpetas tal cual. Detalle completo en `contract.md` de esta misma feature. Aprendizaje transferible: las decisiones de estructura son mucho más baratas de revertir antes de que el resto del sistema (aquí, Unity) empiece a asumirlas.
