# Contrato de feature — M00.F03: Unity + VS Code + Claude Code

| Campo | Valor |
|---|---|
| ID | M00.F03 |
| Módulo | M00 — Configuración |
| Estado | In-Progress |
| Issue Jira | [CMX-4](https://fereblanco.atlassian.net/browse/CMX-4) |
| Rama Git | `main` (excepción de M00, ver `modules/M00/features/M00.F02/contract.md` sección 3) |
| Fecha contrato | 2026-07-21 |

## 1. Objetivo

Dejar operativo el proyecto Unity de ComplexLab dentro de `code/`, con el Editor configurado para versionar bien en Git (serialización de texto, meta files visibles), VS Code enlazado como editor externo con soporte C#, Unity Test Framework instalado con un test de humo, y el ciclo completo Cowork↔Code↔Unity probado de punta a punta con un script trivial.

M00 es un módulo sin código propio (ver `modules/M00/README.md`): sus fases de implementación/pruebas se sustituyen por ejecución/verificación, pero el contrato define entregables y criterios de aceptación igual que en una feature de software.

## 2. Conceptos a aprender

Estructura de un proyecto Unity (`Assets/`, `ProjectSettings/`, `Packages/`, `Library/`), render pipelines (Built-in vs. URP vs. HDRP) y por qué URP es la opción por defecto en Unity 6, serialización de assets (binaria vs. texto) y su impacto en control de versiones, archivos `.meta` y GUIDs, Unity Test Framework (NUnit, Edit Mode vs. Play Mode), integración de Unity con editores externos (Visual Studio Code, Omnisharp/C# Dev Kit).

## 3. Alcance

**Incluye:**

- T01 — Instalar Unity Hub + versión LTS *(hecho: Unity Hub 3.19.5, Unity 6000.5.3f1 LTS)*.
- T02 — Crear el proyecto Unity dentro de `code/`, plantilla **Universal 3D** (URP), con Force Text serialization y Visible Meta Files aplicados desde la creación, y un esqueleto mínimo de carpetas en `Assets/`.
- T03 — Configurar VS Code como editor externo de Unity (extensión C#, IntelliSense funcionando).
- T04 — Instalar Unity Test Framework y crear un test de humo (Edit Mode) en verde.
- T05 — Probar el ciclo Claude Code en `code/`: pedirle un script trivial, compilar en Unity, verificar, borrar.
- T06 — Documentar el flujo de trabajo Cowork↔Code↔Unity en `documentation/TOOLS-GUIDE.md`.

**No incluye:**

- El catálogo completo de convenciones C#/Assets (namespaces, responsabilidades, patrones) — eso es M00.F04, que ya lo tiene en su alcance (`CMX-25`, `CMX-26`). Aquí solo se crea un esqueleto mínimo de carpetas para que el proyecto no nazca vacío; M00.F04 lo formaliza y amplía.
- Cerrar el punto pendiente de M00.F02.T05 sobre el matiz de higiene de commits específico de Unity (escenas/prefabs marcados como modificados sin cambios reales) — se verifica y documenta una vez haya un proyecto real con el que probarlo, pero es una actualización de `code/CLAUDE.md` vía M00.F02, no una tarea nueva aquí.
- Configuración de exportación a WebGL — Fere ha señalado que puede interesarle exportar algunas piezas a WebGL para su web más adelante (encaja con el candidato de publicación del roadmap). No se instala el módulo de build WebGL todavía porque no hay contenido que exportar; se anota como dependencia futura, probablemente de la primera feature que produzca una pieza visual terminada (M01 en adelante).

## 4. Entregables (adaptación de especificación funcional)

- **ED-01:** Proyecto Unity en `code/` (plantilla Universal 3D / URP), abre sin errores en Unity 6000.5.3f1.
- **ED-02:** `ProjectSettings/EditorSettings.asset` con `Asset Serialization Mode = Force Text` y `Visible Meta Files` habilitados.
- **ED-03:** Esqueleto inicial de `Assets/` (ver sección 5).
- **ED-04:** VS Code configurado como External Script Editor en Unity, con la extensión C# instalada y funcionando (autocompletado/IntelliSense).
- **ED-05:** Unity Test Framework instalado (paquete `com.unity.test-framework`), con una Test Assembly en Edit Mode y un test de humo en verde.
- **ED-06:** Evidencia del ciclo Claude Code → Unity: un script trivial pedido a Claude Code, compilado sin errores en el Editor, y eliminado tras la prueba (no debe quedar código de humo en el proyecto).
- **ED-07:** Sección nueva en `documentation/TOOLS-GUIDE.md` documentando el flujo Cowork↔Code↔Unity.

## 5. Especificación técnica

### T02 — Creación del proyecto

1. **Ubicación:** directamente en `code/` (no en una subcarpeta) — `code/.gitignore` ya está anclado a esa carpeta desde M00.F02.T03, así que sus patrones (`Library/`, `Temp/`, `Obj/`, etc.) funcionan sin retocar nada.
2. **Plantilla:** *Universal 3D* (URP, proyecto vacío) desde Unity Hub. Ver justificación completa en la conversación previa a este contrato: un proyecto 3D puede comportarse como 2D (cámara ortográfica + sprites/quads) para piezas como Game of Life, pero un proyecto 2D no puede escalar a 3D real (atractores de caos, consciencia) sin cambiar de plantilla a mitad de camino.
3. **Nombre del proyecto** (Product Name en Player Settings): `ComplexLab`.
4. **Ajustes del Editor para Git** (Edit > Project Settings > Editor):
   - **Asset Serialization → Mode: Force Text.** Obliga a Unity a guardar escenas, prefabs y assets como YAML de texto plano en vez de binario, para que Git pueda mostrar diffs legibles línea a línea en vez de "binary files differ".
   - **Version Control → Mode: Visible Meta Files.** Muestra los archivos `.meta` (que contienen los GUIDs que Unity usa para enlazar assets entre sí) directamente en el sistema de archivos, en vez de ocultarlos en una carpeta oculta — así Git los versiona junto a cada asset. Sin esto, mover o renombrar un asset rompe referencias para cualquiera que clone el repo después.
5. **Plataforma de build activa inicial:** PC, Mac & Linux Standalone → Windows (donde trabaja Fere). No se instalan módulos de build adicionales (WebGL incluido) hasta que haga falta exportar algo real — instalarlos ahora sin contenido que probar no aporta nada y alarga la instalación del Editor.
6. **Esqueleto inicial de `Assets/`** (mínimo, ampliable en M00.F04):

   ```
   Assets/
     Scenes/
     Scripts/
     Prefabs/
     Materials/
     ScriptableObjects/
   ```

   Convención de nombres dentro de `Scenes/` y `Scripts/`, alineada con el resto del proyecto: cuando exista la primera feature real (M01.F01), su escena vive en `Assets/Scenes/M01.F01-<nombre-corto>.unity` y sus scripts en `Assets/Scripts/M01.F01/`. No se crean subcarpetas por feature todavía porque no hay ninguna feature real que las llene — se documenta la convención aquí para que M01 la siga desde el primer commit.

### T03 — VS Code como editor externo

En Unity: Edit > Preferences > External Tools > External Script Editor → seleccionar Visual Studio Code. Instalar en VS Code la extensión oficial "C# Dev Kit" (o "C#" de Microsoft si Dev Kit no está disponible) para IntelliSense y depuración. Verificación: abrir un script `.cs` desde el Project window de Unity debe abrir VS Code con autocompletado de la API de Unity funcionando.

### T04 — Unity Test Framework

Instalar vía Package Manager (`com.unity.test-framework`, normalmente ya viene con el proyecto por defecto en Unity 6 — verificar). Crear una Test Assembly en modo Edit Mode (`Assets/Tests/EditMode/`) y un test trivial (p. ej. `Assert.AreEqual(4, 2 + 2)`) para confirmar que el Test Runner funciona antes de que haya lógica real que testear.

### T05 — Ciclo Claude Code

Desde una sesión de Claude Code abierta en `code/`, pedir un script C# trivial (p. ej. un `MonoBehaviour` que loguee un mensaje en `Start()`), verificar que compila sin errores en el Editor de Unity, y borrarlo — el objetivo es probar el ciclo de herramientas, no dejar código de ejemplo en el proyecto.

## 6. Criterios de aceptación

- **CA-01:** El proyecto Unity abre en Unity 6000.5.3f1 sin errores de consola al cargar.
- **CA-02:** `ProjectSettings/EditorSettings.asset` contiene `m_SerializationMode: 2` (Force Text) y `m_LineEndingsForNewScripts` coherente; el modo de meta files es visible (verificable porque los `.meta` aparecen junto a cada asset en `code/Assets/`).
- **CA-03:** `Assets/Scenes/`, `Assets/Scripts/`, `Assets/Prefabs/`, `Assets/Materials/`, `Assets/ScriptableObjects/` existen (pueden estar vacías salvo `.gitkeep` o similar, ya que Git no versiona carpetas vacías).
- **CA-04:** VS Code se abre automáticamente al hacer doble clic en un script desde Unity, con IntelliSense de la API de Unity operativo.
- **CA-05:** El Test Runner de Unity (Window > General > Test Runner) muestra al menos un test de Edit Mode en verde.
- **CA-06:** Un script de prueba pedido a Claude Code compila sin errores en el Editor y se elimina limpiamente después (repo queda sin rastro de él).
- **CA-07:** `documentation/TOOLS-GUIDE.md` tiene una sección nueva describiendo el flujo Cowork↔Code↔Unity, verificable por lectura.
- **CA-08:** Primer commit del proyecto Unity (con `code/.gitignore` ya en vigor) no incluye `Library/`, `Temp/`, `Obj/`, `.vs/` ni ningún otro directorio regenerable — verificable con `git status` tras abrir el proyecto una vez.

## 7. Plan de verificación

- **Manual:** abrir el proyecto, confirmar ausencia de errores en la consola de Unity.
- **Manual:** inspeccionar `ProjectSettings/EditorSettings.asset` y comprobar visualmente que los `.meta` aparecen en el explorador de archivos junto a cada asset.
- **Manual:** ejecutar el Test Runner y confirmar el test de humo en verde.
- **Manual:** ciclo completo Claude Code → compilar en Unity → borrar, observado por Fere.
- **Manual:** `git status` tras el primer commit del proyecto — no debe listar carpetas regenerables como *untracked* ni commiteadas.

## 8. Dependencias

M00.F02 (In-Progress — bloqueante para T02: `code/.gitignore` y `.gitattributes` deben estar en su sitio antes de generar el primer asset binario real, lo cual ya ocurrió en M00.F02.T03). M00.F02.T05 tiene un punto pendiente (matiz de higiene de commits específico de Unity) que depende de que este contrato produzca un proyecto real sobre el que verificarlo — se resuelve actualizando `code/CLAUDE.md` vía M00.F02 una vez T02 esté hecho, no como tarea de esta feature. Bloquea M00.F04 (el catálogo de convenciones y estándares necesita un proyecto real con `Assets/` para tener sentido) y M01 (no hay dónde implementar la primera pieza visual sin proyecto Unity operativo).

## 9. Plan de implementación

| Tarea | Jira | Claude | Estado |
|---|---|---|---|
| T01 Instalar Unity Hub + LTS | [CMX-19](https://fereblanco.atlassian.net/browse/CMX-19) | Manual | Hecho — Unity Hub 3.19.5, Unity 6000.5.3f1 LTS |
| T02 Crear proyecto (Universal 3D, ajustes Editor) | [CMX-20](https://fereblanco.atlassian.net/browse/CMX-20) | Manual | Hecho — proyecto creado en `code/` (movido desde `code/ComplexLab/`), Force Text y Visible Meta Files configurados |
| T03 VS Code como editor externo | [CMX-21](https://fereblanco.atlassian.net/browse/CMX-21) | Manual | Hecho — VS Code configurado como editor externo, extensiones C#/C# Dev Kit/Unity instaladas, IntelliSense y debugging operativos tras instalar .NET 10 SDK (10.0.302) |
| T04 Unity Test Framework + test de humo | [CMX-22](https://fereblanco.atlassian.net/browse/CMX-22) | Code · Sonnet | Hecho — `com.unity.test-framework` (1.7.0) ya venía instalado; creada `Assets/Tests/EditMode/ComplexLab.Tests.EditMode.asmdef` y `SmokeTest.cs`, verificado en verde en el Test Runner |
| T05 Probar ciclo Claude Code | [CMX-23](https://fereblanco.atlassian.net/browse/CMX-23) | Code · Sonnet | Hecho — script trivial creado por Claude Code (2.1.330, Sonnet), compilado sin errores y ejecutado en el Editor, borrado por Claude Code junto con su `.meta`; `git status` limpio |
| T06 Documentar flujo en TOOLS-GUIDE.md | [CMX-24](https://fereblanco.atlassian.net/browse/CMX-24) | Cowork · Sonnet | Pendiente |

## 10. Registro de cambios del contrato

| Fecha | Cambio | Motivo |
|---|---|---|
| 2026-07-21 | Creación del contrato. Feature pasa de Draft a In-Progress (decisión de Fere, previa a este contrato) según su mensaje inicial; se decide plantilla Universal 3D (URP), ubicación directa en `code/`, aplicar Force Text + Visible Meta Files desde la creación, plataforma inicial Windows sin módulo WebGL todavía | T01 ya estaba hecho de forma manual (instalación de herramientas, no implementación); T02 sí es implementación y exige contrato aprobado antes de crear el proyecto, según regla 1 de `CLAUDE.md` raíz. Fere señaló interés futuro en exportar a WebGL, que se anota como dependencia diferida en vez de instalarse sin necesidad inmediata |
| 2026-07-21 | Contrato aprobado por Fere. Estado pasa de Draft a Spec-Approved. Fere ya puede crear el proyecto Unity (T02) siguiendo la sección 5 | Fere aclara que los contratos de instalación/configuración (como este) no necesitan quedar cerrados al 100% sin ambigüedad antes de aprobar — a diferencia de los contratos que dan lugar a código, donde sí exige cero ambigüedad antes de implementar. Ajustes o correcciones posteriores a este contrato se harán sobre la marcha, sin bloquear el arranque de T02 |
| 2026-07-21 | T02 y T03 hechos: proyecto Unity creado en `code/` (Unity Hub lo generó primero en `code/ComplexLab/`, se movió el contenido a `code/` y se borró la subcarpeta vacía), con Force Text y Visible Meta Files aplicados; VS Code configurado como editor externo con extensiones .NET Install Tool, C#, C# Dev Kit, eppz! (C# theme), Git Graph, Unity y Unity Code Snippets. Se instala .NET 10 SDK (10.0.302) tras un aviso de C# Dev Kit ("The .NET SDK cannot be located") — no venía instalado en el sistema y hacía falta aparte de Unity/VS Code para el debugging de C# | Al crear el proyecto, Unity Hub ofreció "GitHub" como Source Control Provider; se rechazó (se eligió "None") porque habría inicializado un repositorio Git propio dentro de `code/`, duplicando el repositorio único ya existente en la raíz desde M00.F02. El aviso de .NET SDK es un fallo habitual de C# Dev Kit cuando no hay .NET SDK instalado a nivel de sistema — no es un problema de configuración de Unity ni de este proyecto |
| 2026-07-30 | T04 hecho: `com.unity.test-framework` (1.7.0) ya venía instalado con el proyecto (Unity 6 lo trae por defecto, sin necesidad de añadirlo a mano); Code creó `Assets/Tests/EditMode/ComplexLab.Tests.EditMode.asmdef` (plantilla estándar "Tests Assembly Folder": `includePlatforms: ["Editor"]`, referencias a `UnityEngine.TestRunner`/`UnityEditor.TestRunner`, `defineConstraints: ["UNITY_INCLUDE_TESTS"]`) y `SmokeTest.cs` dentro de esa carpeta; test verificado en verde en Window > General > Test Runner (pestaña EditMode). Se decide además la convención de nombres para métodos de test (`UnidadBajoPrueba_Escenario_ResultadoEsperado`, patrón Osherove) como excepción explícita a la regla 14 de `code/CLAUDE.md` (métodos empiezan por verbo) — se documenta en `code/CLAUDE.md` | El contrato dejaba pendiente verificar si el test framework venía preinstalado; se confirma que sí. La convención de nombres de test no estaba decidida en el proyecto (`code/CLAUDE.md` la difería explícitamente a M00.F04); al necesitarse ya para el test de humo de T04, Fere pidió decidir el patrón más habitual del sector en vez de esperar a M00.F04, y documentar la decisión aquí y en `code/CLAUDE.md` |
| 2026-07-30 | T04 cerrado definitivamente: corregido `autoReferenced` de `true` a `false` en `ComplexLab.Tests.EditMode.asmdef`, verificado directamente en el Inspector de Unity (checkbox "Any Platform" desmarcado, solo "Editor") | El valor `true` inicial venía de un JSON dictado por Claude de memoria y nunca pasado por un Editor real; Fere replicó el mismo `.asmdef` manualmente desde Unity para aprender el formato y detectó la discrepancia. `false` es además el valor semánticamente correcto para un Test Assembly (evita que `Assembly-CSharp` referencie tipos de test sin querer); se prioriza el valor verificado por el Editor real sobre el dictado de memoria |
| 2026-11-21 | Limpieza previa y primer commit del proyecto Unity (CA-08): se borran `Assets/Tests/EditModeFere/` (réplica manual del ensamblado de tests hecha por Fere para aprender el formato), el material de tutorial de la plantilla URP (`Assets/TutorialInfo/`, `Assets/Readme.asset`) y los `.csproj` obsoletos de ensamblados ya inexistentes; se conserva `Scenes/SampleScene.unity` como escena base. Se commitean `Assets/`, `Packages/` y `ProjectSettings/`; verificado que `Library/`, `Logs/`, `UserSettings/`, `*.csproj` y `*.slnx` quedan ignorados por `code/.gitignore` | CA-08 seguía pendiente: el proyecto existía en disco desde T02 pero nunca se había commiteado. EditModeFere ya había cumplido su función didáctica y duplicaba el test de humo oficial; el tutorial de la plantilla no forma parte del laboratorio. Los `.csproj` los regenera Unity y además están ignorados, así que borrarlos solo evita confusión local |
| 2026-11-21 | T05 hecho: Claude Code (2.1.330, nativo) instalado y con sesión iniciada con la suscripción de claude.ai; desde una sesión en `code/` con Sonnet creó un `MonoBehaviour` trivial que loguea en `Start()`, compiló sin errores en el Editor y Fere vio el mensaje en Play. Claude Code lo borró junto con su `.meta` sin que se le indicara; `git status` limpio tras la prueba. CA-06 y ED-06 verificados | Antes de la prueba `claude` no estaba accesible en la terminal y faltaba iniciar sesión (`claude auth login`); se resuelve sin incidencias. Que Claude Code elimine el `.meta` por iniciativa propia confirma que aplica la regla asset+`.meta` de Unity, pero conviene seguir verificándolo en cada borrado |
