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
