# Contrato de feature — M00.F02: Git y GitHub: repositorio, ramas, conector

| Campo | Valor |
|---|---|
| ID | M00.F02 |
| Módulo | M00 — Configuración |
| Estado | Draft |
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
- T04 — Estrategia de ramas (`main` + `feature/MXX.FYY-short-name`, merge tras fase Testing), documentada en `code/CLAUDE.md`.
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

## 6. Criterios de aceptación

- **CA-00:** `ComplexLab/.git` existe y `git remote -v` sigue apuntando a `complexlab` en GitHub; `repo/` ya no existe; `git log` conserva los 2 commits originales.
- **CA-01:** `git status` en la raíz no muestra ningún archivo de `code/Library/`, `code/Temp/`, `code/Obj/`, `.vs/`, `Thumbs.db` ni `.vscode/` como *untracked* tras generarlos de prueba.
- **CA-02:** `git lfs track` lista exactamente los patrones de la sección 5 (assets + documentos); `.gitattributes` existe en la raíz y está commiteado.
- **CA-03:** Un archivo binario de prueba (p. ej. un `.png` y un `.docx` cualquiera) commiteado se registra como puntero LFS, verificable con `git lfs status` mostrando el archivo en "Git LFS objects to be committed" y `git show <commit>:<archivo> | head -c 200` mostrando la cabecera `version https://git-lfs...` en vez de bytes binarios.
- **CA-04:** `documentation/`, `dossiers/`, `modules/` (incluido este mismo `contract.md`) aparecen en `git log` tras el commit de migración.
- **CA-05:** Commit(s) de migración + `.gitignore`/`.gitattributes` limpios, con mensajes Conventional Commits (p. ej. `chore(M00.F02): flatten repo structure and configure gitignore/lfs`).

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
| T03 Aplanar estructura + `.gitignore` + `.gitattributes`/LFS | [CMX-15](https://fereblanco.atlassian.net/browse/CMX-15) | Code · Sonnet | Pendiente (este contrato) |
| T04 Estrategia de ramas | [CMX-16](https://fereblanco.atlassian.net/browse/CMX-16) | Cowork · Sonnet | Pendiente |
| T05 `code/CLAUDE.md` | [CMX-17](https://fereblanco.atlassian.net/browse/CMX-17) | Code · Sonnet | Pendiente |
| T06 Conector GitHub (opcional) | [CMX-18](https://fereblanco.atlassian.net/browse/CMX-18) | Manual | Pendiente |

## 10. Registro de cambios del contrato

| Fecha | Cambio | Motivo |
|---|---|---|
| 2026-07-09 | Creación del contrato, adaptado a formato ligero de módulo de configuración (sin secciones 4/5 pesadas de spec funcional/técnica de software) | M00.F02 estaba en Draft sin contrato; regla del proyecto exige contrato antes de implementar, incluso en tareas de configuración (ver `modules/M00/README.md`, línea 12) |
| 2026-07-09 | Se amplía el alcance de T03: aplanado de `ComplexLab/` a un único repo Git (elimina `repo/`, introduce `code/`), y se añade categoría "Documentos" (pdf/docx/doc/xlsx/xls/pptx/ppt/epub) al set de LFS | Fere retoma la reconsideración que el propio dossier dejaba abierta sobre separar documentación y código en dos ritmos distintos; se decide unificar antes de que exista proyecto Unity (ventana de migración barata). Los formatos ofimáticos binarios tienen el mismo problema de diffs que los assets Unity, y `dossiers/` los va a producir vía `make-dossier` |
