# Guía de herramientas y modelos

Cómo elegir entre Claude Chat, Cowork y Code, y qué modelo usar en cada caso.

## 1. ¿Qué herramienta?

La pregunta clave: **¿dónde tiene que ocurrir el resultado?**

| Si el resultado es… | Usa | Ejemplos en ComplexLab |
|---|---|---|
| Conocimiento en tu cabeza (conversación, aprendizaje, decisión) | **Chat** | "Explícame la estigmergia", comparar dos patrones, discutir una idea de módulo |
| Archivos y gestión en las carpetas del proyecto | **Cowork** | Contratos, dossiers, roadmap, plantillas, seguimiento en Jira, informes, revisar el estado del proyecto |
| Código en el repositorio | **Code** | Implementar features en Unity, tests, refactors, commits, depurar |

Reglas rápidas:

- Chat no toca tus archivos: úsalo para pensar, no para producir entregables.
- Cowork ve tus carpetas y conectores (Jira, etc.): es el centro de gestión del proyecto. Todo el trabajo de spec, documentación y seguimiento ocurre aquí.
- Code vive en el terminal/VS Code dentro de `code/`: es donde se implementa. Nunca uses Cowork para escribir código ni Code para redactar dossiers — aunque ambos compartan ya el mismo repositorio Git, la frontera de responsabilidad sigue siendo por carpeta.
- Flujo típico de una feature: contrato en **Cowork** → implementación y tests en **Code** → conclusiones y Jira en **Cowork**.

## 2. ¿Qué modelo?

| Modelo | Carácter | Úsalo para |
|---|---|---|
| **Fable 5** | El más capaz; más lento y caro | Arquitectura, diseño de specs complejas, decisiones estructurales, primeras iteraciones de algo nuevo (como esta configuración) |
| **Opus 4.8** | Razonamiento profundo | Algoritmos difíciles, depuración compleja, matemáticas delicadas, revisión de diseño |
| **Sonnet 5** | Caballo de batalla: rápido y muy capaz | Implementación diaria, tests, documentación, dossiers, la mayoría del trabajo |
| **Haiku 4.5** | Muy rápido y barato | Resúmenes, formateo, renombrados, tareas mecánicas, automatizaciones programadas |

Heurística: empieza con Sonnet; sube a Opus/Fable si la tarea exige diseño o razonamiento que Sonnet no resuelve a la primera; baja a Haiku para lo mecánico. Gastar Fable en tareas rutinarias consume tu cuota sin beneficio.

## 3. Agentes y Skills (objetivo de aprendizaje)

- **Skills:** instrucciones reutilizables que Claude carga cuando tocan (ya usas `make-dossier`). Crearemos skills propias cuando detectemos tareas repetitivas (p. ej. "crear contrato de feature").
- **Agentes (subagentes):** instancias de Claude lanzadas para subtareas largas o paralelas (p. ej. verificar un contrato mientras se redacta otro). Se practicarán a partir de M01.
- **Conectores (MCP):** enlaces con apps externas. Jira/Atlassian requiere autorizarlo una vez en la configuración de conectores.

## 4. Flujo Cowork ↔ Code ↔ Unity

Cómo viaja una feature entre las tres herramientas. Los tres comparten **un único repositorio Git** (raíz de `ComplexLab/`); lo que cambia es *quién toca qué carpeta*.

### 4.1. Reparto de responsabilidades

| Pieza | Dónde trabaja | Qué hace | Qué NO hace |
|---|---|---|---|
| **Cowork** | Raíz del proyecto (`modules/`, `documentation/`, `dossiers/`) + Jira | Contratos, dossiers, conclusiones, estado en Jira y en los README de módulo | Escribir código de `code/` |
| **Claude Code** | `code/` (lee `code/CLAUDE.md` automáticamente) | Scripts C#, tests, refactors, commits de código | Redactar contratos o dossiers |
| **Unity Editor** | `code/` (proyecto Unity 6000.5.3f1, URP) | Compila los scripts, ejecuta Play y el Test Runner, regenera `Library/` y los `.meta` | — (es el juez: si no compila aquí, no está hecho) |
| **VS Code** | `code/` | Editor externo de Unity: lectura, IntelliSense, depuración y Git visual | — |

**Por qué esta frontera:** cada herramienta carga su propio `CLAUDE.md` (raíz para Cowork, `code/CLAUDE.md` para Code), así que cada una arranca con las reglas de su carpeta y no mezcla convenciones. Los idiomas también van separados: español en documentación de proyecto, inglés en código y commits.

### 4.2. El ciclo de una feature

1. **Cowork — contrato.** Se redacta `contract.md` desde la plantilla y se aprueba (estado *Spec-Approved*). Sin contrato aprobado no se implementa nada (regla 1 de `CLAUDE.md`). Se crean las subtareas en Jira.
2. **Cowork → Git — rama.** Desde M01, rama `feature/MXX.FYY-short-name`. En M00 se commitea directo a `main` (excepción del contrato de M00.F02, sección 3).
3. **Code — implementación.** Sesión de Claude Code abierta **dentro de `code/`**, para que cargue `code/CLAUDE.md`. Crea o edita scripts y tests siguiendo las convenciones de C#.
4. **Unity — verificación.** Al volver al Editor, Unity recompila solo; la consola debe quedar sin errores. Se prueba con **Play** y con **Window > General > Test Runner** (pestaña EditMode). Si falla, se vuelve al paso 3 con el error concreto.
5. **Code — commit.** Conventional Commits en inglés con el ámbito de la feature (`feat(M01.F01): …`). Antes de cada commit se revisa qué archivos se añaden (regla 9 de `code/CLAUDE.md`: nada de "stage all" por defecto).
6. **Cowork — cierre.** Se actualiza el estado en el contrato y en el README del módulo, se registran las conclusiones y se mueven los tickets en Jira.

### 4.3. Reglas de Unity que conviene recordar

- **Cada asset tiene su `.meta`** (guarda el GUID con el que Unity enlaza referencias). Si se borra, mueve o renombra un archivo, el `.meta` viaja con él. Claude Code lo aplica por su cuenta, pero hay que comprobarlo en cada borrado.
- **Force Text + Visible Meta Files** están activos: escenas y prefabs son YAML legible y los `.meta` se versionan. Por eso los diffs de Git sirven.
- **No se commitea lo regenerable:** `Library/`, `Logs/`, `UserSettings/`, `*.csproj` y `*.slnx` están en `code/.gitignore`. Unity los recrea al abrir el proyecto.
- **Compila en Unity, no solo en VS Code:** IntelliSense avisa de errores de sintaxis, pero solo el Editor valida contra el proyecto real.

### 4.4. Qué modelo en cada paso

| Paso | Herramienta | Modelo habitual |
|---|---|---|
| Diseño de la spec o de una arquitectura nueva | Cowork / Chat | Fable (diseño) |
| Redactar contratos, documentación, Jira | Cowork | Sonnet |
| Implementar y testear en `code/` | Code | Sonnet (Opus si hay algoritmo o depuración difícil) |
| Renombrados, formateo, tareas mecánicas | Cualquiera | Haiku |

### 4.5. Verificado en la práctica

Este flujo se ensayó en M00.F03.T05 (CMX-23): desde una sesión de Code en `code/` se pidió un `MonoBehaviour` trivial, Unity lo compiló y lo ejecutó en Play, y Code lo borró junto con su `.meta`, dejando `git status` limpio.
