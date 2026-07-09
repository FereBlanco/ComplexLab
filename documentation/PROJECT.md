# ComplexLab — Documento maestro

**Versión:** 1.0 · **Fecha:** 2026-07-08 · **Autor:** Fere (con Claude)

## 1. Visión

ComplexLab es un laboratorio personal de aprendizaje. El vehículo son piezas visuales de software en Unity sobre vida artificial, consciencia, sistemas complejos y matemáticas (números complejos, fractales, caos, geometría). El fin real es doble: dominar esos conceptos y dominar la forma de trabajar con IA (Claude Chat, Cowork y Code) en un proyecto gestionado con rigor profesional.

## 2. Objetivos

1. Manejar la IA en general y Claude en particular (herramientas, modelos, agentes, skills).
2. Configurar y supervisar un proyecto complejo: planificación, seguimiento y pruebas.
3. Completar un proyecto no-code siguiendo Spec Driven Development (SDD).
4. Aprender sobre vida artificial, consciencia, surgimiento de la vida, simulación de enjambres.
5. Aprender matemáticas aplicadas: números complejos, fractales, teoría del caos, geometría.
6. Aplicar los patrones de diseño apropiados, en especial los propios de Unity.
7. Conectar Claude con otras aplicaciones (Jira, VS Code, Unity, Git, Blender).
8. Publicar resultados seleccionados en blog y LinkedIn.

## 3. Estructura del proyecto

`ComplexLab/` entero es un único repositorio Git (enlazado a GitHub `complexlab`): código y documentación comparten la misma historia. Decisión tomada en M00.F02 (2026-07-09), revisando la separación original documentada en `modules/M00/features/M00.F02/dossier.md`.

```
ComplexLab/                  Raíz del repositorio Git
├── .gitignore              Exclusiones de SO/editor (todo el repo)
├── .gitattributes          Patrones Git LFS (assets binarios y documentos ofimáticos)
├── documentation/          Documentación general del proyecto
│   ├── PROJECT.md          Este documento
│   ├── TOOLS-GUIDE.md      Guía de elección Chat/Cowork/Code y modelos
│   ├── ROADMAP.md          Roadmap de módulos
│   └── templates/          Plantillas (contrato, módulo, dossier, conclusiones)
├── dossiers/               Dossiers temáticos (uno por carpeta de encargo)
├── modules/
│   └── MXX/                Un módulo por temática
│       ├── README.md       Ficha del módulo (a partir de templates/modulo.md)
│       └── features/
│           └── MXX.FYY/    Contrato e información específica de cada feature
└── code/                   Proyecto Unity, con su propio .gitignore (específico de Unity)
```

## 4. Convenciones

- **Módulos:** `MXX` (M00, M01…). M00 = fundamentos/configuración. Cada módulo tiene su `README.md`.
- **Features:** `MXX.FYY` (p. ej. `M01.F01`). Carpeta en `modules/MXX/features/MXX.FYY/` con al menos `contract.md` y, al cerrar, `conclusions.md`.
- **Tareas:** `MXX.FYY.TZZ` (p. ej. `M00.F01.T04`). Cada nivel hereda el prefijo del padre y añade su tipo (`T` de tarea) con numeración de dos dígitos (`T01`, `T02`…). Una tarea es la unidad mínima de trabajo dentro de una feature; por ahora el término engloba tanto trabajo técnico como las fases del ciclo de vida SDD (si en el futuro conviene distinguirlos, se revisará). Cada tarea se corresponde con una subtarea de Jira.
- **Dossier de feature:** cuando una feature lo merezca, se abre con un `dossier.md` en su carpeta que explica el qué, el cómo y el porqué (conceptos teóricos incluidos), y se actualiza con conclusiones al cerrar. En este proyecto el porqué es tan importante como el qué: el objetivo es aprender.
- **Idiomas:** documentación, contratos y dossiers en **español**; código, identificadores, nombres de rama y mensajes de commit en **inglés**.
- **Git:** ramas `feature/MXX.FYY-short-name`; commits estilo Conventional Commits (`feat:`, `fix:`, `docs:`, `test:`). El código vive solo en `code/`.
- **Jira:** una épica por módulo (ej. CMX-1 para M00), una historia por feature (ej. CMX-2 para M00.F01), una subtarea por tarea (ej. CMX-12 para M00.F01.T05). El ID Jira se anota en: (1) el README del módulo en una tabla de features, (2) el ROADMAP del módulo en la columna "Jira" de cada tarea, (3) el dossier de la feature (en la cabecera), (4) el contrato de la feature (en metadatos o cabecera). La trazabilidad es bidireccional: desde Jira al documento, desde el documento al ticket.
- **Estados de una feature:** `Draft → Spec-Approved → In-Progress → Testing → Integrated → Documented → Closed`.

## 5. Metodología: Spec Driven Development

Principio: **nada se implementa sin un contrato aprobado**. El contrato (spec) es la fuente de verdad; el código debe cumplirlo, y si durante la implementación surge un cambio, se actualiza primero el contrato.

### Ciclo de vida de una feature

| Fase | Entregable | Criterio de salida |
|---|---|---|
| 1. Definición / Contrato | `contract.md` (plantilla) | Contrato revisado y aprobado por ti |
| 2. Plan de implementación | Sección "Plan" del contrato o issue Jira | Tareas descompuestas y estimadas |
| 3. Implementación | Código en `code/` | Cumple la spec; compila sin warnings |
| 4. Pruebas unitarias | Tests en Unity Test Framework | Tests del contrato en verde |
| 5. Integración | Feature integrada en la escena/proyecto común | Sin regresiones en features previas |
| 6. Pruebas globales | Checklist de pruebas manuales + tests de integración | Checklist completada |
| 7. Documentación | Docs técnicos + capturas/vídeo | Otro lector podría reproducirlo |
| 8. Conclusiones | `conclusions.md` (plantilla) | Lecciones registradas; decisión sobre publicación |

### Roles

- **Tú (Fere):** product owner y supervisor. Apruebas contratos, validas pruebas, decides publicaciones.
- **Claude:** redacta contratos y dossiers, implementa (vía Claude Code), propone pruebas, documenta. Nunca salta fases.

## 6. Publicaciones

Módulos o features destacados pueden generar entradas de blog o posts de LinkedIn. Antes de la primera publicación se creará `documentation/PUBLISHING.md` con estilo, tono, estructura y checklist (pendiente — se abordará cuando exista el primer candidato).

## 7. Integraciones previstas

| Herramienta | Uso | Vía |
|---|---|---|
| Jira | Épicas, historias, seguimiento | Conector Atlassian. Sitio: fereblanco.atlassian.net (usuario fere.blanco@gmail.com), proyecto `CMX` (ComplexLab, Kanban team-managed) |
| Git/GitHub | Control de versiones de todo `ComplexLab/` | Claude Code + conector GitHub |
| VS Code + Unity | Desarrollo | Claude Code en `code/` |
| Blender | Modelos 3D puntuales | Manual; assets exportados a `code/` (vía Git LFS) |

## 8. Convención: Anotación de claves Jira

Para mantener trazabilidad bidireccional entre Jira y los documentos del proyecto, cada módulo, feature y tarea lleva su clave Jira anotada en lugares estándar:

### En el README del módulo
Tabla de features (ej. `modules/M00/README.md`, línea 24): cada feature incluye un enlace a su ticket historia.

```
| ID | Nombre | Estado |
|---|---|---|
| M00.F01 | Conexión y estructura de Jira · [CMX-2](https://fereblanco.atlassian.net/browse/CMX-2) | In-Progress |
```

### En el ROADMAP del módulo
Tabla de tareas (ej. `modules/M00/ROADMAP.md`, cada feature): columna "Jira" con enlaces a subtareas.

```
| # | Tarea | Jira | Claude | Verificación |
| 1 | Autorizar conector | [CMX-8](https://fereblanco.atlassian.net/browse/CMX-8) | Manual | ✔ |
```

### En el dossier de la feature
Cabecera (ej. `modules/M00/features/M00.F01/dossier.md`, línea 5): enlace al ticket historia.

```
| Feature | M00.F01 — Conexión y estru