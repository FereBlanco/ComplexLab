# Dossier M00.F01 — Organización del trabajo: Kanban y la jerarquía de Jira

| Campo | Valor |
|---|---|
| Feature | M00.F01 — Conexión y estructura de Jira ([CMX-2](https://fereblanco.atlassian.net/browse/CMX-2)) |
| Fecha | 2026-07-08 |
| Estado del dossier | Final — feature cerrada (Done en Jira, 2026-07-09) |

## 1. Qué vamos a hacer

Montar el sistema de seguimiento del proyecto en Jira: un tablero Kanban con una jerarquía de tickets (épicas, historias, subtareas) que refleje la estructura de ComplexLab (módulos, features, tareas), y conectarlo con Claude para que la gestión diaria se haga desde Cowork.

## 2. Los conceptos

### El tablero Kanban

Kanban (看板, "señal visual" en japonés) nace en las fábricas de Toyota en los años 50 como sistema para regular el flujo de producción: una tarjeta física acompañaba a cada pieza e indicaba cuándo reponer material. El desarrollo de software lo adoptó en los 2000 con la misma idea central: **hacer visible el trabajo y limitar cuánto está en marcha a la vez**.

Un tablero Kanban son columnas que representan estados (en nuestro caso: To Do → In Progress → In Review → Done) por las que las tarjetas (tickets) fluyen de izquierda a derecha. Sus dos reglas esenciales:

1. **Todo el trabajo está en el tablero.** Si no hay tarjeta, no existe. Esto elimina el trabajo invisible, que es donde mueren los proyectos personales.
2. **Límite de trabajo en curso (WIP limit).** Pocas cosas en In Progress a la vez. Terminar vale más que empezar.

La alternativa habitual es **Scrum**, que organiza el trabajo en sprints: ciclos cerrados (típicamente 2 semanas) con un compromiso de entrega al inicio y una revisión al final. Scrum impone ritmo; Kanban impone flujo.

### La jerarquía de tickets en Jira

Jira organiza el trabajo en niveles anidados. De mayor a menor:

| Nivel | Nombre Jira | Qué representa | Vida típica |
|---|---|---|---|
| 1 | **Épica** (Epic) | Un objetivo grande que agrupa trabajo relacionado | Semanas o meses |
| 0 | **Historia** (Story) | Una unidad de valor completa, descrita desde el punto de vista de quien la usa ("como usuario quiero X para Y") | Días |
| 0 | **Tarea** (Task) | Como la historia, pero trabajo técnico sin "usuario": configurar, migrar, investigar | Días |
| 0 | **Bug** | Un defecto a corregir | Horas o días |
| −1 | **Subtarea** (Subtask) | Un paso concreto dentro de una historia o tarea. Siempre tiene padre | Horas |

Historia, tarea y bug viven en el mismo nivel (0): son hermanos, no jerarquía. La diferencia es semántica — qué tipo de trabajo describen — y eso afecta a cómo se redactan y se miden. Las subtareas cuelgan siempre de un ticket de nivel 0; las épicas agrupan tickets de nivel 0.

### Los estados y el flujo

Cada ticket está siempre en exactamente un estado. Nuestro tablero tiene cuatro: **To Do** (comprometido pero no empezado), **In Progress** (alguien trabaja en ello ahora), **In Review** (hecho, pendiente de validación) e **Done** (terminado según su criterio de verificación). Mover una tarjeta es un acto explícito: nada pasa a Done sin que su verificación se cumpla.

## 3. Por qué ComplexLab se organiza así

### El mapeo elegido

| ComplexLab | Jira | Ejemplo |
|---|---|---|
| Módulo MXX | Épica | M00 — Configuración = CMX-1 |
| Feature MXX.FYY | Historia | M00.F01 = CMX-2 |
| Tarea MXX.FYY.TZZ | Subtarea | M00.F01.T04 = CMX-11 |

El mapeo es natural porque las escalas coinciden: un módulo dura semanas (épica), una feature días (historia), una tarea horas (subtarea). Además cada nivel de Jira tiene su espejo en las carpetas del proyecto: épica ↔ `modules/MXX/README.md`, historia ↔ `contract.md` de la feature, y las subtareas ↔ las fases del ciclo de vida SDD. El tablero y la documentación cuentan la misma historia desde dos ángulos: Jira dice *cuándo y en qué estado*, los contratos dicen *qué y cómo*.

### Por qué Kanban y no Scrum

Elegimos Kanban por tres razones concretas de este proyecto:

1. **Es un proyecto de una persona sin fechas de entrega.** Los sprints de Scrum sirven para sincronizar equipos y comprometer entregas; en solitario, esa ceremonia es sobrecarga sin beneficio. Kanban da la disciplina (visibilidad, flujo, límite de WIP) sin el ritual.
2. **El aprendizaje no se planifica bien por sprints.** Habrá features que exijan parar y estudiar un dossier, y semanas con más o menos tiempo disponible. Un flujo continuo absorbe esa variabilidad; un sprint fallido solo genera frustración.
3. **El WIP limit protege contra el mayor riesgo del proyecto:** la dispersión. Con ocho módulos apetecibles, la tentación de abrir frentes es constante. La regla "termina la feature antes de abrir otra" está codificada en el tablero.

Aun así, Scrum queda pendiente de conocerse: si algún módulo futuro quiere ensayarlo (los tableros team-managed permiten activar sprints), será un experimento deliberado y documentado.

### Por qué cada ticket lleva herramienta y modelo

Cada subtarea indica en su descripción qué opción de Claude usar (Chat/Cowork/Code o Manual) y qué modelo (Fable/Opus/Sonnet/Haiku). Esto convierte el tablero en material de estudio: al cerrar cada ticket se puede juzgar si la elección fue acertada, y ese juicio acumulado es exactamente el criterio que este proyecto quiere entrenar. La lógica general está en `documentation/TOOLS-GUIDE.md`.

### Qué ganamos y qué nos cuesta

Ganamos trazabilidad (cada commit citará su ticket), hábito profesional (el mismo sistema que usan los equipos reales) y un registro histórico honesto del proyecto. El coste es burocracia: mantener tickets al día en un proyecto de una persona requiere disciplina, y parte de esa carga se delega en Claude (crear, transicionar y consultar tickets desde Cowork es una sola frase). Si en algún momento el tablero deja de reflejar la realidad, eso será una señal — y materia para las conclusiones de este dossier.

## 4. Conclusiones

Al volcar las tareas a Jira surgió una lección sobre la pr