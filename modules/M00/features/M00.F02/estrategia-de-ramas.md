# Estrategia de ramas de ComplexLab

Documento de aprendizaje — M00.F02.T04. No es un contrato ni está pensado para que lo lea Claude Code (eso es `code/CLAUDE.md`); es la explicación en claro de las reglas que hemos decidido y por qué, para que las tengas a mano.

## Las reglas, de un vistazo

1. Cada feature vive en su propia rama: `feature/MXX.FYY-nombre-corto`.
2. Todas las tareas de esa feature se hacen sobre la misma rama — no se abre una rama nueva por cada tarea.
3. Antes de mergear, se rebasea la rama sobre la última versión de `main`.
4. El merge a `main` siempre pasa por un Pull Request (PR) en GitHub, nunca un push directo.
5. El tipo de merge es "Create a merge commit" — nunca "squash" ni "rebase and merge".
6. Las ramas no se borran después de mergear.
7. `main` no tiene protección todavía (se puede seguir empujando directo, aunque en la práctica ya no lo harás salvo para M00).

## Por qué cada regla

### Una rama por feature

Es la unidad de trabajo que ya usa todo el proyecto: el contrato, el ticket de Jira y la rama comparten el mismo identificador (`M01.F01`). Si abrieras una rama por tarea (`M01.F01.T01`, `M01.F01.T02`...) tendrías que ir mergeando ramas entre sí antes de llegar a `main`, sin ganar nada a cambio — la tarea no es una unidad que se prueba o se aprueba por separado, la feature sí.

### Rebase antes de mergear

Cuando trabajas en una rama unos días, `main` puede haber avanzado con otros cambios mientras tanto (tuyos de otra feature, o míos). Rebasear significa "recolocar" tus commits como si los hubieras escrito *después* de esos cambios nuevos, en vez de mezclarlos con un commit de fusión intermedio. El resultado práctico: la rama llega al PR ya actualizada y sin sorpresas, y de paso practicas rebase de verdad — resolviendo los conflictos que aparezcan — en vez de solo leer sobre él.

Es la parte más delicada de Git de las que vamos a tocar: rebase reescribe commits (les cambia el identificador interno), así que si alguna vez compartes una rama con alguien más no se rebase después de que otro ya la haya usado. Aquí trabajas solo, así que no hay ese riesgo.

### Pull Request en vez de push directo

Podrías simplemente hacer `git push origin main` desde tu rama y ahorrarte el paso. La razón de pasar por PR aunque trabajes solo: queda un registro en GitHub de cada feature que entró (con su diff completo, comentarios si los hay, fecha), y es el sitio donde yo puedo revisar el código antes de que aterrice en `main` — ahora mismo pegándote comentarios sobre el diff, y en cuanto actives el conector de GitHub (T06), leyendo el PR directamente. Es, además, el flujo real que usarías en cualquier equipo: practicarlo aquí no cuesta nada extra.

### Merge commit, no squash ni rebase-merge

Hay tres formas de que GitHub incorpore un PR a `main`:

- **Merge commit** (la que usamos): añade todos los commits de la rama a `main`, más uno nuevo que marca la fusión. Se ve claramente en el historial dónde entró cada feature.
- **Squash**: aplasta todos los commits de la rama en uno solo. Main queda muy limpio, pero pierdes el detalle de cómo llegaste ahí — y ese detalle es justo lo que en un proyecto de aprendizaje interesa conservar.
- **Rebase and merge**: añade los commits de la rama a `main` uno a uno, sin commit de fusión, dejando el historial en línea recta. Es elegante pero borra la marca visible de "aquí empezó y terminó esta feature".

Como ya vamos a rebasear la rama antes del PR (regla 3), un merge commit al final te da lo mejor de los dos mundos: historial de la rama limpio y ordenado, más una marca clara en `main` de cada feature que entró.

### Las ramas no se borran

Esto lo pediste tú expresamente, y tiene sentido con tu objetivo de practicar Git: cada rama fusionada queda como un caso real al que volver — para comparar historiales, probar un rebase distinto sobre ella, o simplemente recordar cómo se resolvió tal conflicto. El coste de mantenerlas es prácticamente cero (una rama es solo un puntero a un commit, no una copia del proyecto), así que no hay urgencia por limpiar. Si algún día una rama concreta ya no aporta nada, se borra a propósito — nunca como efecto colateral de un merge.

### `main` sin protección todavía

GitHub permite bloquear `main` para que nadie pueda hacer push directo y todo tenga que pasar por PR. No lo activamos aún porque M00 (este mismo módulo de configuración) todavía necesita commits directos a `main` para tareas que no son desarrollo de producto — es la excepción que ya documentamos en el contrato de esta feature. En cuanto M00 se cierre y empiece M01 (el primer módulo con código real), tiene sentido revisar si activarla.

## Con qué herramienta hacer cada cosa

Vas a manejar Git sobre todo desde el plugin de Git de VS Code, con la terminal como apoyo puntual. Ninguna regla de arriba depende de una herramienta concreta, pero como referencia:

| Acción | Cómoda desde VS Code | Cómoda desde CLI |
|---|---|---|
| Crear rama, hacer commits, push | Sí, panel de Source Control | Sí |
| Rebase interactivo con conflictos | Aceptable, pero el editor de conflictos de VS Code está pensado para merges, no para rebase paso a paso | Más cómoda: `git rebase -i origin/main`, resolver, `git rebase --continue` |
| Abrir el PR | Sí, si tienes la extensión "GitHub Pull Requests" instalada; si no, la web de GitHub | La web de GitHub (no hay comando de Git puro para "abrir PR", es una función de la plataforma) |
| Mergear el PR con "Create a merge commit" | Desde la web o la extensión de GitHub | No aplica — es una acción de GitHub, no de Git local |

## Lo que queda pendiente

- **T05** añadirá a `code/CLAUDE.md` las convenciones de C#/Unity y el resto de reglas de commits — esta estrategia de ramas ya está incorporada allí.
- **T06** (opcional) autoriza el conector de GitHub en Claude, que es lo que me permitirá revisar tus PRs directamente desde Cowork en vez de que me pegues el diff.
- La protección de `main` queda como decisión pendiente para cuando cerremos M00.
