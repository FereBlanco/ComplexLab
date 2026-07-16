# Estrategia de ramas de ComplexLab

Documento de aprendizaje — M00.F02.T04. No es un contrato ni está pensado para que lo lea Claude Code (eso es `code/CLAUDE.md`); es la explicación en claro de las reglas que hemos decidido y por qué, para que las tengas a mano.

## Las reglas, de un vistazo

1. Cada feature vive en su propia rama: `feature/MXX.FYY-nombre-corto`.
2. Todas las tareas de esa feature se hacen sobre la misma rama — no se abre una rama nueva por cada tarea.
3. Antes de mergear, se actualiza la rama trayéndose los cambios de `main` (merge de `main` hacia la rama, o simplemente resolviendo conflictos al abrir el PR) — **sin rebase**. Es el flujo que ya conoces de tu empresa (y de Plastic SCM).
4. El merge a `main` siempre pasa por un Pull Request (PR) en GitHub, nunca un push directo.
5. El tipo de merge es "Create a merge commit" — nunca "squash" ni "rebase and merge".
6. Las ramas no se borran después de mergear.
7. `main` no tiene protección todavía (se puede seguir empujando directo, aunque en la práctica ya no lo harás salvo para M00).

## Por qué cada regla

### Una rama por feature

Es la unidad de trabajo que ya usa todo el proyecto: el contrato, el ticket de Jira y la rama comparten el mismo identificador (`M01.F01`). Si abrieras una rama por tarea (`M01.F01.T01`, `M01.F01.T02`...) tendrías que ir mergeando ramas entre sí antes de llegar a `main`, sin ganar nada a cambio — la tarea no es una unidad que se prueba o se aprueba por separado, la feature sí.

### Actualizar con main antes de mergear, sin rebase

Cuando trabajas en una rama unos días, `main` puede haber avanzado con otros cambios mientras tanto. Antes de abrir o cerrar el PR, te traes esos cambios a tu rama (`git merge main` sobre tu rama, o simplemente dejas que el propio PR te avise de conflictos y los resuelves ahí), y sigues adelante. Es el flujo estándar, el mismo que usabas con Plastic SCM y el que se usa en tu empresa — nunca reescribe commits, nunca hace falta forzar un push, y el historial cuenta exactamente lo que pasó: dos líneas de trabajo en paralelo que se juntan en un punto.

Más abajo tienes una sección aparte explicando qué es *rebase* y por qué decidimos no usarlo por defecto, aunque es útil conocerlo.

### Pull Request en vez de push directo

Podrías simplemente hacer `git push origin main` desde tu rama y ahorrarte el paso. La razón de pasar por PR aunque trabajes solo: queda un registro en GitHub de cada feature que entró (con su diff completo, comentarios si los hay, fecha), y es el sitio donde yo puedo revisar el código antes de que aterrice en `main` — ahora mismo pegándote comentarios sobre el diff, y en cuanto actives el conector de GitHub (T06), leyendo el PR directamente. Es, además, el flujo real que usarías en cualquier equipo: practicarlo aquí no cuesta nada extra.

### Merge commit, no squash ni rebase-merge

Hay tres formas de que GitHub incorpore un PR a `main`:

- **Merge commit** (la que usamos): añade todos los commits de la rama a `main`, más uno nuevo que marca la fusión. Se ve claramente en el historial dónde entró cada feature, y es exactamente lo que describías tú mismo: la rama sale de un commit, tiene sus propios commits, y se reúne con main en uno nuevo.
- **Squash**: aplasta todos los commits de la rama en uno solo. Main queda muy limpio, pero pierdes el detalle de cómo llegaste ahí — y ese detalle es justo lo que en un proyecto de aprendizaje interesa conservar.
- **Rebase and merge**: añade los commits de la rama a `main` uno a uno, sin commit de fusión, dejando el historial en línea recta. Es elegante pero borra la marca visible de "aquí empezó y terminó esta feature".

### Las ramas no se borran

Esto lo pediste tú expresamente: cada rama fusionada queda como un caso real al que volver — para comparar historiales, practicar sobre ella, o simplemente recordar cómo se resolvió tal conflicto. El coste de mantenerlas es prácticamente cero (una rama es solo un puntero a un commit, no una copia del proyecto), así que no hay urgencia por limpiar. Si algún día una rama concreta ya no aporta nada, se borra a propósito — nunca como efecto colateral de un merge.

### `main` sin protección todavía

GitHub permite bloquear `main` para que nadie pueda hacer push directo y todo tenga que pasar por PR. No lo activamos aún porque M00 (este mismo módulo de configuración) todavía necesita commits directos a `main` para tareas que no son desarrollo de producto — es la excepción que ya documentamos en el contrato de esta feature. En cuanto M00 se cierre y empiece M01 (el primer módulo con código real), tiene sentido revisar si activarla.

## Qué es rebase, y por qué no lo usamos por defecto

No forma parte de las reglas de arriba, pero conviene entenderlo — es un concepto central de Git y es probable que te lo encuentres, aunque sea leyéndolo en otro sitio.

Rebase significa "cambiar la base" de una rama: en vez de decir "mis commits salieron de un punto antiguo de main", Git reescribe esos commits como si hubieran salido del punto más reciente. El contenido final es el mismo, pero la historia queda ordenada como si hubieras empezado a trabajar más tarde de lo que realmente empezaste.

Ejemplo. Creas tu rama `feature` en el commit `B` de main, y haces dos commits propios, `D` y `E`. Mientras tanto, en `main` se añade el commit `C`.

```
Antes de rebase:

main:     A---B---C
               \
feature:        D---E
```

Si haces `git rebase main` sobre tu rama, Git aparta `D` y `E`, mueve la punta de tu rama hasta `C`, y los vuelve a aplicar uno a uno. Como su "padre" ya no es `B` sino `C`, son commits nuevos — mismo contenido, distinto identificador interno. Por eso se llaman `D'` y `E'` (D-prima, E-prima):

```
Después de rebase:

main:     A---B---C
                    \
feature:             D'---E'
```

Si después de esto mergeas con "create a merge commit" (regla 5), el resultado en main sería:

```
main:     A---B---C---D'---E'---F
```

Compáralo con lo que realmente vamos a hacer (sin rebase, regla 3): main se queda en `A-B-C`, tu rama sale de `B` con `D` y `E` sin tocar, y al mergear aparece un commit `F` con dos padres (`C` y `E`):

```
main:     A---B---C-------F
               \         /
feature:        D-------E
```

Ambos resultados son válidos y Git los soporta igual de bien. La diferencia es puramente de forma: con rebase, la historia de tu rama queda "estirada" en línea recta antes de unirse (`D'-E'` seguidos de `C`); sin rebase, queda el rombo típico que muestra que `D-E` y `C` pasaron en paralelo de verdad — que es lo que honestamente ocurrió.

La razón principal por la que se evita rebase por defecto en muchos equipos (incluido el tuyo) es que reescribe commits: si esa rama ya la había descargado alguien más, esa persona se queda con commits "fantasma" que ya no existen en tu versión, y hace falta forzar el push (`git push --force-with-lease`) para actualizarla en el remoto. En un repo donde trabajas solo ese riesgo no existe, pero preferimos usar el mismo flujo que ya conoces y que usa tu empresa — simple, seguro por defecto, y sin comandos que reescriban historia. Si en algún momento te apetece practicar rebase como ejercicio deliberado sobre una rama concreta, es buen momento para hacerlo con calma, sabiendo que no es la política del proyecto.

## Con qué herramienta hacer cada cosa

Vas a manejar Git sobre todo desde el plugin de Git de VS Code, con la terminal como apoyo puntual. Ninguna regla de arriba depende de una herramienta concreta.

| Acción | Cómoda desde VS Code | Cómoda desde CLI |
|---|---|---|
| Crear rama, hacer commits, push | Sí, panel de Source Control | Sí |
| Traer main y resolver conflictos | Sí, VS Code tiene buen editor de conflictos de merge | Sí, `git merge main` + resolver + commit |
| Abrir el PR | Sí, si tienes la extensión "GitHub Pull Requests" instalada; si no, la web de GitHub | La web de GitHub (no hay comando de Git puro para "abrir PR", es una función de la plataforma) |
| Mergear el PR con "Create a merge commit" | Desde la web o la extensión de GitHub | No aplica — es una acción de GitHub, no de Git local |

## Lo que queda pendiente

- **T05** añadirá a `code/CLAUDE.md` las convenciones de C#/Unity y el resto de reglas de commits — esta estrategia de ramas ya está incorporada allí.
- **T06** (opcional) autoriza el conector de GitHub en Claude, que es lo que me permitirá revisar tus PRs directamente desde Cowork en vez de que me pegues el diff.
- La protección de `main` queda como decisión pendiente para cuando cerremos M00.
- Las dos guías oficiales de Unity sobre control de versiones (2022 y Unity 6 edition) ya se revisaron: no contradicen nada de esta estrategia. Quedan transcritas en `documentation/references/` por si hace falta consultarlas.
