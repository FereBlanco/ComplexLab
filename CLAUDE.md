# ComplexLab

Laboratorio de aprendizaje: piezas visuales en Unity sobre vida artificial, consciencia, enjambres, fractales y caos, gestionado con Spec Driven Development y Claude (Chat/Cowork/Code).

## Referencias obligadas

- `documentation/PROJECT.md` — visión, convenciones, ciclo de vida de features.
- `documentation/ROADMAP.md` — módulos previstos.
- `documentation/TOOLS-GUIDE.md` — qué herramienta/modelo usar.
- `documentation/templates/` — usar SIEMPRE estas plantillas para contratos, módulos, dossiers y conclusiones.

## Reglas para Claude

1. **SDD estricto:** no implementar nada sin contrato aprobado (`contract.md` en estado Spec-Approved). Si la implementación exige cambios, actualizar primero el contrato y su registro de cambios.
2. **Idiomas:** documentación de proyecto (PROJECT.md, ROADMAP.md, contratos, dossiers, conclusiones) en español; código, ramas, commits, comentarios de código, nombres de funciones/variables y documentación del código (README.md de Git, docstrings) en inglés.
3. **Ubicaciones:** código solo en `code/`; contratos en `modules/MXX/features/MXX.FYY/`; dossiers en `dossiers/`; documentación general en `documentation/`. Todo vive en un único repositorio Git (raíz de `ComplexLab/`, sin carpeta `repo/` intermedia) — decisión de M00.F02.
4. **Nombres:** módulos `MXX`, features `MXX.FYY`, tareas `MXX.FYY.TZZ`, ramas `feature/MXX.FYY-short-name`, commits Conventional Commits.
5. **Rol didáctico:** Fere está aprendiendo; al proponer una solución, explicar brevemente el porqué (patrón elegido, herramienta elegida) en lugar de solo ejecutar.
6. **Estados de feature:** Draft → Spec-Approved → In-Progress → Testing → Integrated → Documented → Closed. Mantener actualizado el estado en el contrato y en el README del módulo.
