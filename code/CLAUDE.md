# code/ — Convenciones de código

Este archivo lo lee Claude Code automáticamente al trabajar dentro de `code/` (el proyecto Unity). Convenciones de C#/Unity, formato de commits y estructura de `Assets/` se añadirán en M00.F02.T05, cuando exista contenido real que convencionar. Esta primera sección cubre la estrategia de ramas (M00.F02.T04).

## Estrategia de ramas

1. **Una rama por feature, no por tarea.** `feature/MXX.FYY-short-name`, creada desde `main` actualizado. Todas las tareas `MXX.FYY.TZZ` de esa feature commitean sobre la misma rama.
2. **Rebase antes de mergear.** Antes de abrir/actualizar el PR: `git fetch origin` + `git rebase origin/main` sobre la rama de feature, resolviendo conflictos en local.
3. **Merge siempre vía Pull Request en GitHub**, nunca push directo de una rama de feature a `main`. El PR se abre cuando la feature alcanza la fase Testing de su contrato.
4. **Tipo de merge: "Create a merge commit"** (equivalente a `git merge --no-ff`) — nunca squash ni rebase-merge.
5. **Las ramas no se borran tras el merge.** Quedan como material de consulta y práctica de Git; se borran deliberadamente si sobran, no por defecto.
6. **`main` sin protección todavía.** M00 sigue necesitando push directo para tareas de configuración. Se reconsidera al empezar M01.
7. **Excepción M00:** mientras dure el módulo de configuración, los commits de tareas de M00 van directos a `main` (sin rama de feature), documentado en `modules/M00/features/M00.F02/contract.md` sección 3. Desde M01 en adelante aplican las reglas 1-6 sin excepción.
8. **Mensajes de commit:** Conventional Commits con el ámbito de la feature, p. ej. `feat(M01.F01): add cell grid simulation`.

Detalle y porqués de estas reglas, en lenguaje llano: `modules/M00/features/M00.F02/estrategia-de-ramas.md`. Especificación técnica completa: `modules/M00/features/M00.F02/contract.md`, sección 5 (T04).
