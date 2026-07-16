# Best practices for project organization and version control — Unity 6 edition

> Nota de proceso (2026-07-10): transcripción en Markdown del e-book oficial de Unity Technologies (2024, edición Unity 6), a partir del PDF `Best_practices_for_project_organization_and_version_control_Unity_6_edition.pdf` aportado por Fere. Es la actualización de `unity-version-control-and-project-organization.md` (edición 2022, ya archivada) — comparten la inmensa mayoría del contenido, con "Plastic SCM" renombrado a "Unity Version Control (UVCS)" y algunas secciones nuevas. Se omiten imágenes a petición expresa. **También se omiten, por instrucción de Fere, las secciones sobre Unity Cloud/DevOps que ComplexLab no usa:** Asset Manager, Build Automation, "Get started with UVCS in Unity 6" (guía paso a paso de la herramienta propia de Unity, que no usamos — trabajamos con Git/GitHub), Unity Gaming Services y Unity Grow (analítica, monetización, adquisición de usuarios — no aplica a un laboratorio de aprendizaje personal). Se referencian brevemente para que quede constancia de que existen, sin transcribir su contenido.

Source: Unity Technologies, © 2024.

## Contenido idéntico a la edición 2022 (no repetido aquí)

Las siguientes secciones son texto prácticamente idéntico al de `unity-version-control-and-project-organization.md` — mismas explicaciones, mismos ejemplos, mismo código — con el único cambio sistemático de "Plastic SCM" → "Unity Version Control (UVCS)". Consultar ese archivo para el contenido completo:

- Introducción, "Source control vs version control", todos los "Foundational concepts" (cómo funciona el control de versiones, por qué usarlo, centralizado vs distribuido, flujo de trabajo típico, tabla de términos clave).
- Organización de proyecto: estructura de carpetas (los dos ejemplos `Assets/...`), carpetas vacías, el archivo `.meta`, estándares de nombres, optimización de flujo de trabajo (dividir assets, Presets).
- Estándares de código: namespaces, cabecera estándar, plantillas de script de Unity (`81-C# Script-NewBehaviourScript.cs.txt`, keywords `#SCRIPTNAME#`/`#NOTRIM#`, script `KeywordReplace`).
- Perforce, Apache Subversion: descripciones sin cambios relevantes.
- Editor project settings para Git ("Visible Meta Files"), qué ignorar, trabajo con archivos grandes.
- Buenas prácticas de control de versiones: *commit little, commit often*, mensajes de commit limpios, **evitar commits indiscriminados**, "get the latest", conocer tu herramienta, ramas de feature y Git Flow, pull requests.
- Conclusión y recursos adicionales (misma lista, con "Plastic SCM" en vez de "UVCS" en los enlaces heredados de 2022).

## Contenido nuevo o revisado en esta edición

### UI Toolkit formatting conventions (nueva sección)

UI Toolkit usa UXML, inspirado en tecnologías web, así que sigue **kebab-case** (minúsculas con guiones) para nombres de clase e ID — la misma convención que CSS — en vez de Camel/Pascal case.

| | Recomendado | No recomendado |
|---|---|---|
| Clases | `.menu-bar-blue` | `ButtonBlue` |
| IDs de Visual Element | `main-nav-image` | — |

Motivos: los selectores CSS no distinguen mayúsculas/minúsculas (Camel/Pascal case genera confusión), la especificación HTML recomienda atributos en minúsculas, y kebab-case es más legible con nombres de varias palabras. Lo importante, como con cualquier convención, es la consistencia en todo el proyecto.

*Relevancia para ComplexLab:* ninguna todavía — no se ha decidido si el proyecto usará UI Toolkit. Anotado para cuando se aborde UI (probablemente M00.F04 o una feature de módulo posterior).

### Naming standards — referencia añadida

La tabla de estándares de nombres es idéntica a la de 2022, pero añade una recomendación: consultar el e-book de Unity *"Create a C# style guide: Write cleaner code that scales"* (basado en las convenciones de estilo de Microsoft C#, con la guía de Google C# como alternativa) para naming, formato, clases, métodos y comentarios más allá de GameObjects.

*Relevancia para ComplexLab:* es casi con toda seguridad el documento que Fere aportará para **T05** (buenas prácticas de desarrollo de código) — queda anotado como referencia cruzada esperada.

### Servicios de organización de proyecto — Asset Manager y Build Automation (omitido)

Nueva sección sobre el **Asset Manager** de Unity (gestión de assets en la nube, indexado entre proyectos) y **Build Automation** (compilación en la nube, historial de builds). Ambos son servicios de Unity Cloud de pago/gestionados — fuera del alcance de ComplexLab, que usa Git/GitHub y compilación local. No se transcribe el contenido; se referencian solo para constancia.

### Unity Version Control (antes Plastic SCM)

Mismo contenido que la sección "Plastic SCM" de 2022 (Gluon, diff de imágenes, Branch Explorer, code review, Mergebot, modos centralizado/distribuido/multi-site), con el nombre actualizado y una precisión nueva: hay **tres formas de acceder a UVCS** — cliente de escritorio UVCS, integración directa vía Unity Hub, o navegador web (Unity Cloud).

### Get started with UVCS in Unity 6 (omitido)

Sección extensa (páginas 62-89 del PDF) con guía paso a paso de la herramienta UVCS: crear/unirse a un repositorio, invitar miembros del equipo, hacer check-in, cliente de escritorio, Gluon, ramas, resolución de conflictos, reglas de merge, bloqueo de archivos, gestión del repositorio. Es documentación de producto de una herramienta que ComplexLab no usa (usamos Git/GitHub, no UVCS) — no se transcribe.

### Build the foundation for your live game (omitido)

Sección de cierre sobre Unity Gaming Services (multiplayer, cuentas, gestión de contenido, crash reporting, economía de juego, analítica) y Unity Grow (adquisición de usuarios, monetización). Es material de marketing/producto de servicios en la nube de Unity — irrelevante para un laboratorio de aprendizaje personal sin publicación comercial. No se transcribe.
