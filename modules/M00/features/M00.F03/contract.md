# Contrato de feature — M00.F03: Unity + VS Code + Claude Code

| Campo | Valor |
|---|---|
| ID | M00.F03 |
| Módulo | M00 — Configuración |
| Estado | Draft |
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
| T02 Crear proyecto (Universal 3D, ajustes Editor) | [CMX-20](https://fereblanco.atlassian.net/browse/CMX-20) | Manual | Pendiente — bloqueada hasta aprobar este contrato |
| T03 VS Code como editor externo | [CMX-21](https://fereblanco.atlassian.net/browse/CMX-21) | Manual | Pendiente |
| T04 Unity Test Framework + test de humo | [CMX-22](https://fereblanco.atlassian.net/browse/CMX-22) | Code · Sonnet | Pendiente |
| T05 Probar ciclo Claude Code | [CMX-23](https://fereblanco.atlassian.net/browse/CMX-23) | Code · Sonnet | Pendiente |
| T06 Documentar flujo en TOOLS-GUIDE.md | [CMX-24](https://fereblanco.atlassian.net/browse/CMX-24) | Cowork · Sonnet | Pendiente |

## 10. Registro de cambios del contrato

| Fecha | Cambio | Motivo |
|---|---|---|
| 2026-07-21 | Creación del contrato. Feature pasa de Draft a In-Progress (decisión de Fere, previa a este contrato) según su mensaje inicial; se decide plantilla Universal 3D (URP), ubicación directa en `code/`, aplicar Force Text + Visible Meta Files desde la creación, plataforma inicial Windows sin módulo WebGL todavía | T01 ya estaba hecho de forma manual (instalación de herramientas, no implementación); T02 sí es implementación y exige contrato aprobado antes de crear el proyecto, según regla 1 de `CLAUDE.md` raíz. Fere señaló interés futuro en exportar a WebGL, que se anota como dependencia diferida en vez de instalarse sin necesidad inmediata |
