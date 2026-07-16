# Contrato de feature — M00.F02: Git y GitHub: repositorio, ramas, conector

| Campo | Valor |
|---|---|
| ID | M00.F02 |
| Módulo | M00 — Configuración |
| Estado | In-Progress |
| Issue Jira | [CMX-3](https://fereblanco.atlassian.net/browse/CMX-3) |
| Rama Git | `main` (excepción documentada, ver sección 3) |
| Fecha contrato | 2026-07-09 |

## 1. Objetivo

Dejar `ComplexLab/` entero convertido en un único repositorio Git enlazado a GitHub (`complexlab`), con el código Unity en `code/` y la documentación/contratos/dossiers compartiendo la misma historia: con el ruido generado por Unity ignorado, los binarios (assets y documentos ofimáticos) gestionados con Git LFS, la estrategia de ramas documentada, y las convenciones de código escritas en `code/CLAUDE.md`.

M00 es un módulo sin código (ver `modules/M00/README.md`): sus fases de implementación/pruebas se sustituyen por ejecución/verificación, pero el contrato sigue definiendo entregables y criterios de aceptación igual que en una feature de software.

## 2. Conceptos a aprender

Git (repositorio, commit, rama, remoto), Git LFS y el problema de los binarios en control de versiones, `.gitignore` vs `.gitattributes`, Conventional Commits, estrategia de ramas trunk-based ligera, pull requests como punto de revisión.

Detalle conceptual completo en `features/M00.F02/dossier.md`.

## 3. Alcance

**Incluye:**

- T01 — Repositorio remoto GitHub `complexlab` *(hecho)*.
- T02 — Git inicializado en `repo/` y enlazado al remoto *(hecho)*.
- T03 — Aplanar la estructura: eliminar el nivel `repo/`, mover `.git` a la raíz de `ComplexLab/`, y el futuro proyecto Unity vivirá en `code/` (subcarpeta hermana de `documentation/`, `dossiers/`, `modules/`). `.gitignore` raíz (SO/editor) + `code/.gitignore` (Unity, sin modificar respecto al oficial de GitHub, anclado a `code/`) + `.gitattributes` raíz con Git LFS para binarios, incluyendo documentos ofimáticos.
- T04 — Estrategia de ramas (`main` + `feature/MXX.FYY-short-name`, merge tras fase Testing), documentada en `code/CLAUDE.md`. Las ramas **no se borran automáticamente tras el merge** (decisión 2026-07-10, ver dossier sección 3): se conservan como material de práctica de Git; se borran deliberadamente cuando sobren, no por defecto.
- T05 — `code/CLAUDE.md` con convenciones (C#/Unity, commits, ramas).
- T06 — Conector GitHub en Claude (opcional).

**No incluye:**

- Crear el proyecto Unity en sí (`M00.F03.T02`).
- Ajustes del Editor de Unity (Force Text, meta files visibles): son configuración de `ProjectSettings/` que solo se puede aplicar una vez exista el proyecto Unity (M00.F03). Se anota aquí como dependencia pendiente para no perderla — **hueco detectado**: el roadmap actual no tiene una tarea explícita para estos dos ajustes; sugiero añadirla a M00.F03 al redactar su contrato.
- Instalación o configuración de Git/Git LFS como herramienta (se asume ya instalado; si no, es tarea manual previa).
- Migrar historia ya existente con `git lfs migrate`: no aplica — el repo solo tiene 2 commits, ambos de texto (`.gitignore`, `README.md`), no hay binarios que rescatar de la historia.

**Cambio de estructura (decisión 2026-07-09):** el dossier de esta feature ya planteaba la separación `repo/` (código) vs `documentation/`/`dossiers/`/`modules/` (fuera de Git) como "discutible" y proponía reconsiderarla si dolía. Se reconsidera ahora, antes de que exista proyecto Unity (ventana barata para mover la raíz del repo): se elimina el nivel `repo/`, `ComplexLab/` pasa a ser la raíz del repositorio Git, y el proyecto Unity (cuando se cree en M00.F03) vivirá en `code/` — documentación, dossiers, contratos y código comparten una sola historia, pero el código sigue teniendo su propio subárbol con su propio `.gitignore` anclado. El detalle técnico de la migración está en la sección 5.

**Excepción de rama:** durante M00 no hay desarrollo de producto, solo configuración, así que T03–T06 se commitean directo a `main` en vez de abrir `feature/M00.F02-git-github`. La propia estrategia de ramas se define en T04 — abrir una rama para definir cómo se abren ramas sería circular. La convención de "todo en rama" empieza a aplicarse desde M01.

## 4. Entregables (adaptación de especificación funcional)

- **ED-00:** Migración de estructura — `.git` movido de `repo/.git` a `ComplexLab/.git`; `documentation/`, `dossiers/`, `modules/` quedan versionados en la misma historia; el contenido actual de `repo/` (solo `.gitignore` y `README.md` de momento, no hay proyecto Unity aún) se traslada a `code/`.
- **ED-01:** `.gitignore` raíz — exclusiones de sistema operativo y editor.
- **ED-02:** `code/.gitignore` — Unity.gitignore oficial de GitHub sin modificar, anclado a `code/`.
- **ED-03:** `.gitattributes` raíz — patrones LFS para las categorías de binarios previstas (assets Unity + documentos ofimáticos).
- **ED-04:** `code/CLAUDE.md` — convenciones de código (T05, fuera del alcance inmediato de T03 pero parte del contrato de la feature).
- **ED-05:** Sección de estrategia de ramas documentada (T04).
- **ED-06:** `README.md` raíz actualizado — diagrama de estructura sin el nivel `repo/`, con `code/` en su lugar (hoy hay dos copias idénticas del README, una en la raíz y otra en `repo/`; se consolida en una sola tras el aplanado).

## 5. Especificación técnica

### T03 — Aplanado de estructura

1. Mover `repo/.git` → `ComplexLab/.git` (conserva historia y remoto `origin` sin cambios: la URL de GitHub no depende de la ruta local).
2. Mover `repo/README.md` y `repo/.gitignore` → `code/README.md` (a fusionar/descartar, ver ED-06) y `code/.gitignore`.
3. `git add` de `documentation/`, `dossiers/`, `modules/`, `code/`, `CLAUDE.md`, `README.md` en la nueva raíz; primer commit conjunto.
4. Carpeta `repo/` queda vacía y se elimina.

### T03 — `.gitignore` raíz (SO/editor)

```
# OS
Thumbs.db
ehthumbs.db
desktop.ini
.DS_Store

# Editor
.vscode/
```

Windows por ser el sistema de Fere; macOS por si acaso (portabilidad, coste cero); `.vscode/` completa porque en este proyecto no se comparte configuración de editor vía repo — si en M00.F03 se decide versionar `extensions.json` recomendadas, se excepciona esa ruta entonces. Este archivo aplica a todo el repo (documentación incluida), por eso vive en la raíz y no dentro de `code/`.

### T03 — `code/.gitignore` (Unity)

Punto de partida: Unity.gitignore oficial de `github/gitignore` (ya presente hoy en `repo/.gitignore`, analizado — cubre `Library/`, `Temp/`, `Obj/`, `Build/`, `Logs/`, `UserSettings/`, cachés de IDE, y los ignorados específicos de Addressables/Visual Scripting). Se mueve a `code/.gitignore` sin recortes ni cambios de contenido — sus patrones anclados con `/` (p. ej. `/[Ll]ibrary/`) siguen siendo correctos porque ahora se evalúan relativos a `code/`, que es la futura raíz del proyecto Unity.

### T03 — `.gitattributes` raíz (Git LFS)

Categorías y extensiones a trackear vía LFS — set completo, declarado antes de que existan assets reales (ver justificación en la conversación previa: barato declarar ahora, caro migrar historia después):

```
# Images
*.png filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.tif filter=lfs diff=lfs merge=lfs -text
*.tiff filter=lfs diff=lfs merge=lfs -text
*.psd filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text

# 3D models
*.fbx filter=lfs diff=lfs merge=lfs -text
*.obj filter=lfs diff=lfs merge=lfs -text
*.blend filter=lfs diff=lfs merge=lfs -text

# Audio
*.wav filter=lfs diff=lfs merge=lfs -text
*.mp3 filter=lfs diff=lfs merge=lfs -text
*.ogg filter=lfs diff=lfs merge=lfs -text

# Video
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.mov filter=lfs diff=lfs merge=lfs -text

# Fonts
*.ttf filter=lfs diff=lfs merge=lfs -text
*.otf filter=lfs diff=lfs merge=lfs -text

# Documents (dossiers .docx, referencias .pdf, hojas de cálculo)
*.pdf filter=lfs diff=lfs merge=lfs -text
*.docx filter=lfs diff=lfs merge=lfs -text
*.doc filter=lfs diff=lfs merge=lfs -text
*.xlsx filter=lfs diff=lfs merge=lfs -text
*.xls filter=lfs diff=lfs merge=lfs -text
*.pptx filter=lfs diff=lfs merge=lfs -text
*.ppt filter=lfs diff=lfs merge=lfs -text
*.epub filter=lfs diff=lfs merge=lfs -text
```

Motivo de la categoría "Documents": `.docx`/`.xlsx`/`.pptx`/`.epub` son contenedores ZIP de XML (OOXML); `.doc`/`.xls`/`.ppt` son binario legacy puro. Ninguno es diffable por Git de forma útil, y cada guardado reescribe el archivo entero — mismo problema que una imagen o un audio. Relevante sobre todo para `dossiers/`, donde la skill `make-dossier` generará y regenerará `.docx` a partir del material de cada carpeta de encargo.

No se trackean vía LFS: `.md` (toda la documentación, contratos y dossiers-fuente son texto y deben poder diferenciarse línea a línea — es la razón de ser de tenerlos en Git), `.blend1` (ya ignorado, backup temporal de Blender), `.meta` (siempre texto), assets serializados como texto (`.unity`, `.prefab`, `.asset`, `.mat` con Force Text activado).

Este archivo vive en la raíz (no dentro de `code/`) porque cubre binarios tanto de `code/` (assets Unity) como de `dossiers/` (documentos ofimáticos) — Git LFS no necesita que `.gitattributes` esté cerca de los archivos que gobierna, solo que esté en algún ancestro del path.

### T04 — Estrategia de ramas (decisiones 2026-07-10)

Reglas fijadas, a partir de M01 (M00 sigue con la excepción de la sección 3):

1. **Una rama por feature, no por tarea.** `feature/MXX.FYY-short-name`, creada desde `main` actualizado. Todas las tareas `MXX.FYY.TZZ` de esa feature commitean sobre la misma rama.
2. **Actualizar con `main` antes de mergear, sin rebase.** Antes de abrir/cerrar el PR, traer los cambios de `main` a la rama de feature (`git merge main`, o resolviendo conflictos que señale el propio PR) y resolver conflictos en local. Es el flujo estándar (el mismo que Fere ya usaba con Plastic SCM y usa en su empresa): nunca reescribe commits, nunca requiere push forzado. *(Corregido 2026-07-10: la primera versión de esta regla pedía rebase por defecto; se revirtió tras revisar ventajas/inconvenientes — ver registro de cambios y `estrategia-de-ramas.md`.)*
3. **Merge siempre vía Pull Request en GitHub**, nunca push directo de una rama de feature a `main`. El PR se abre cuando la feature alcanza la fase Testing de su contrato (criterio de cierre ya definido en el ciclo SDD, `PROJECT.md` sección 5).
4. **Tipo de merge: "Create a merge commit"** (equivalente a `git merge --no-ff`) en el botón de GitHub — nunca squash ni rebase-merge. Conserva la historia completa de la rama dentro de `main` y deja un commit de merge visible.
5. **Las ramas no se borran tras el merge** (ya decidido y documentado en el dossier, sección 3) — quedan como material de consulta y práctica de Git.
6. **`main` sin protección todavía.** Se reconsidera al cerrar M00 y empezar M01 — hasta entonces sigue permitiendo push directo para tareas de configuración (excepción de la sección 3 de este contrato).
7. **Revisión de PR:** hasta que M00.F02.T06 (conector GitHub) esté hecho, la revisión es manual — Fere se revisa el propio diff, o pide a Claude Chat/Cowork que lo revise pegando el diff o leyendo el archivo. La apertura del PR en sí (web o VS Code) no depende del conector — es una función de GitHub, no de Claude.
8. **Herramientas:** Fere gestiona Git desde el plugin de Git de VS Code y, en ocasiones, la CLI. Las reglas de arriba son agnósticas de herramienta; todas funcionan bien desde VS Code.

Entregable de T04: esta sección (fuente de verdad operativa para Claude, junto con `code/CLAUDE.md`) más un documento de aprendizaje aparte para Fere — `modules/M00/features/M00.F02/estrategia-de-ramas.md` — con las mismas reglas en lenguaje llano y sus porqués. No es un contrato ni está pensado para que lo lea Claude Code.

## 6. Criterios de aceptación

- **CA-00:** `ComplexLab/.git` existe y `git remote -v` sigue apuntando a `complexlab` en GitHub; `repo/` ya no existe; `git log` conserva los 2 commits originales.
- **CA-01:** `git status` en la raíz no muestra ningún archivo de `code/Library/`, `code/Temp/`, `code/Obj/`, `.vs/`, `Thumbs.db` ni `.vscode/` como *untracked* tras generarlos de prueba.
- **CA-02:** `git lfs track` lista exactamente los patrones de la sección 5 (assets + documentos); `.gitattributes` existe en la raíz y está commiteado.
- **CA-03:** Un archivo binario de prueba (p. ej. un `.png` y un `.docx` cualquiera) commiteado se registra como puntero LFS, verificable con `git lfs status` mostrando el archivo en "Git LFS objects to be committed" y `git show <commit>:<archivo> | head -c 200` mostrando la cabecera `version https://git-lfs...` en vez de bytes binarios.
- **CA-04:** `documentation/`, `dossiers/`, `modules/` (incluido este mismo `contract.md`) aparecen en `git log` tras el commit de migración.
- **CA-05:** Commit(s) de migración + `.gitignore`/`.gitattributes` limpios, con mensajes Conventional Commits (p. ej. `chore(M00.F02): flatten repo structure and configure gitignore/lfs`).
- **CA-06:** `code/CLAUDE.md` existe y contiene la sección de estrategia de ramas con las 8 reglas de arriba.
- **CA-07:** `modules/M00/features/M00.F02/estrategia-de-ramas.md` existe, en español llano, con las reglas y sus porqués — verificable por lectura (no es un criterio automatizable).
- **CA-08:** Las dos guías oficiales de Unity sobre control de versiones (2022 y Unity 6 edition) están transcritas en `documentation/references/` y su contenido contrastado contra las decisiones de T04 — sin hallazgos que contradigan lo decidido.
- **CA-09:** `code/CLAUDE.md` contiene una regla de higiene de commits (no usar "stage all" por defecto) — adelanto de T05, verificable por lectura.
- **CA-10:** La guía oficial de Unity *Writing Cleaner Code That Scales* está transcrita en `documentation/references/` y contrastada contra la configuración actual, con puntos alineados y puntos de decisión identificados.
- **CA-11:** `code/CLAUDE.md` contiene la sección "Convenciones de código C#" (nombres, formato, organización de clases, comentarios) con las 6 decisiones de T05 (llaves Allman, indentación con tabuladores, prefijo `_campo`, ancho de línea 100, `.editorconfig` creado, TDD/testing diferido a M00.F04) — verificable por lectura.
- **CA-12:** `code/.editorconfig` existe y aplica indentación con tabuladores, llaves Allman y ancho de línea 100, coherente con CA-11 — verificable por lectura y, cuando exista código real, por comportamiento en VS Code.

## 7. Plan de verificación

- **Manual:** generar carpetas `code/Library/` y `code/Temp/` de mentira y comprobar que `git status` las ignora; limpiar después.
- **Manual:** copiar un `.png` y un `.docx` de prueba, `git add` + `git commit`, verificar con `git lfs status` y `git show` que viajaron como puntero.
- **Manual:** confirmar en GitHub (web) que el LFS storage del repo registra ambos objetos tras el `push`, y que `documentation/`, `dossiers/`, `modules/` son visibles en el repo remoto.

## 8. Dependencias

M00.F01 (Closed — conector Jira operativo). T01/T02 de esta misma feature (ya hechos). Bloquea M00.F03 (no tiene sentido crear el proyecto Unity dentro de un repo sin `.gitignore`/LFS listos, o entrarían binarios sin trackear desde el primer commit) y bloquea cualquier commit futuro de documentación (dossiers, contratos) hasta que la migración esté cerrada, para no tener trabajo a medio mover.

## 9. Plan de implementación

| Tarea | Jira | Claude | Estado |
|---|---|---|---|
| T01 Repositorio remoto GitHub | [CMX-13](https://fereblanco.atlassian.net/browse/CMX-13) | Manual | Hecho |
| T02 Git inicializado y enlazado | [CMX-14](https://fereblanco.atlassian.net/browse/CMX-14) | Code · Haiku | Hecho |
| T03 Aplanar estructura + `.gitignore` + `.gitattributes`/LFS | [CMX-15](https://fereblanco.atlassian.net/browse/CMX-15) | Code · Sonnet | Hecho, verificado y pusheado (`e172d49` en origin/main) |
| T04 Estrategia de ramas | [CMX-16](https://fereblanco.atlassian.net/browse/CMX-16) | Cowork · Sonnet | Hecho |
| T05 `code/CLAUDE.md` | [CMX-17](https://fereblanco.atlassian.net/browse/CMX-17) | Code · Sonnet | Avanzado (higiene de commits + convenciones C# completas + `.editorconfig`); falta estructura de `Assets/` |
| T06 Conector GitHub (opcional) | [CMX-18](https://fereblanco.atlassian.net/browse/CMX-18) | Manual | Pendiente |

## 10. Registro de cambios del contrato

| Fecha | Cambio | Motivo |
|---|---|---|
| 2026-07-09 | Creación del contrato, adaptado a formato ligero de módulo de configuración (sin secciones 4/5 pesadas de spec funcional/técnica de software) | M00.F02 estaba en Draft sin contrato; regla del proyecto exige contrato antes de implementar, incluso en tareas de configuración (ver `modules/M00/README.md`, línea 12) |
| 2026-07-09 | Se amplía el alcance de T03: aplanado de `ComplexLab/` a un único repo Git (elimina `repo/`, introduce `code/`), y se añade categoría "Documentos" (pdf/docx/doc/xlsx/xls/pptx/ppt/epub) al set de LFS | Fere retoma la reconsideración que el propio dossier dejaba abierta sobre separar documentación y código en dos ritmos distintos; se decide unificar antes de que exista proyecto Unity (ventana de migración barata). Los formatos ofimáticos binarios tienen el mismo problema de diffs que los assets Unity, y `dossiers/` los va a producir vía `make-dossier` |
| 2026-07-10 | Contrato aprobado por Fere; T03 implementado y verificado en local: `.git` movido a la raíz, `code/` creado, `.gitignore`/`.gitattributes` en su sitio, `git-lfs` instalado en el sandbox (no venía preinstalado) y registrado con `git lfs install --local`. CA-00, CA-01, CA-02, CA-03, CA-04, CA-05 verificados en local. Estado pasa a In-Progress. Nota técnica: la carpeta `code/` quedó vacía porque el proyecto Unity aún no existe (M00.F03); su `.gitignore` solo tiene efecto una vez haya contenido dentro | Cierre del ciclo contrato → implementación → verificación de esta iteración de T03. CA-00/CA-03 relativos a GitHub (push, LFS storage remoto) quedan pendientes: el sandbox de ejecución no tiene credenciales de GitHub guardadas — el `push` lo debe ejecutar Fere desde su máquina |
| 2026-07-10 | Nota técnica añadida al registro: durante esta sesión se detectó un desfase de sincronización entre la vista de archivos de Cowork (Windows) y el montaje del sandbox Linux — ediciones recién guardadas aparecían truncadas al leerlas desde bash. Se resolvió reescribiendo los archivos afectados directamente desde el mismo proceso bash que ejecuta Git, antes de commitear, evitando el cruce de esa frontera | Evitar commitear contenido corrupto/truncado en la historia de Git; documentado como aprendizaje operativo para sesiones futuras con esta combinación de herramientas |
| 2026-07-10 | Push ejecutado por Fere (`e172d49` en `origin/main`); CA-00 y CA-03 quedan completamente verificados (incluida la parte remota) | Cierra el ciclo de T03: contrato → implementación → verificación local → push → verificación remota |
| 2026-07-10 | Se corrige el alcance de T04 (aún Pendiente): las ramas ya no se borran automáticamente tras el merge. Actualizado también en el dossier, sección 3 | Fere quiere conservar ramas como material de práctica de Git (rebase, comparación de historiales, recuperación); borrado deliberado más adelante si sobran, no por defecto |
| 2026-07-10 | T04 desarrollado: merge commit --no-ff, vía Pull Request, rebase de la rama sobre `origin/main` antes de mergear, sin protección de `main` todavía, revisión de PR manual hasta T06. Añadidas CA-06 y CA-07 | Decisiones tomadas con Fere pregunta por pregunta (tipo de merge, PR vs local, rebase previo, protección de main). Se añade CA-07 porque el entregable didáctico (`estrategia-de-ramas.md`) es un criterio de aceptación tan válido como los técnicos, aunque no sea automatizable |
| 2026-07-10 | Se revierte la regla 2 (rebase obligatorio antes de mergear) por una actualización simple con `main`, sin rebase | Fere preguntó por las ventajas reales del rebase frente al flujo de merge que ya usa en su empresa (y usaba con Plastic SCM). Tras comparar ambos flujos se decide adoptar el mismo merge simple sin rebase, dejando rebase documentado como concepto opcional en `estrategia-de-ramas.md` |
| 2026-07-10 | Se revisan las dos guías oficiales de Unity sobre control de versiones (2022 y Unity 6 edition), transcritas en `documentation/references/`; se añade regla de higiene de commits ("no stage all por defecto") a `code/CLAUDE.md` como adelanto de T05, con la parte específica de Unity pendiente para M00.F03. Se añaden CA-08 y CA-09. T04 pasa a Hecho; T05 pasa de Pendiente a Iniciado | Cierre de T04: no quedaba ningún punto pendiente de las guías por revisar. La regla de higiene de commits se adelanta porque es una práctica general de Git (no depende de que exista proyecto Unity), mientras que su matiz específico de Unity sí depende de verificarlo sobre un proyecto real |
| 2026-07-16 | Se revisa la tercera guía oficial de Unity (*Writing Cleaner Code That Scales*), transcrita en `documentation/references/`. Se deciden 6 convenciones de código C# (llaves Allman, indentación con tabuladores, prefijo `_campo` para privados, ancho de línea 100, `.editorconfig` creado ya, TDD/testing diferido a M00.F04) y se añaden a `code/CLAUDE.md` junto con nombres/formato/organización de clases/comentarios extraídos de la guía. Se crea `code/.editorconfig`. Se añaden CA-10, CA-11 y CA-12. T05 pasa de Iniciado a Avanzado (queda solo la estructura de `Assets/`, pendiente de M00.F03 por no existir aún proyecto Unity real) | Fere decidió las 6 convenciones pregunta por pregunta; tabuladores fue una elección deliberada pese a no ser la recomendación por defecto (justificada: buen soporte de VS Code, mitigado por `.editorconfig`). Estructura de `Assets/` se deja fuera porque depende de contenido real que aún no existe |
