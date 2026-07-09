# Roadmap de módulos

Orden por dificultad creciente. Cada módulo une un tema científico/matemático con patrones de Unity y una habilidad de Claude nueva. El roadmap es una propuesta viva: se revisa al cerrar cada módulo.

| Módulo | Tema | Conceptos | Patrones Unity | Habilidad Claude |
|---|---|---|---|---|
| **M00** | Configuración (sin código) | Conexiones Jira/Git/Unity, estándares, patrones, ecosistema Claude — ver `modules/M00/ROADMAP.md` | Estructura de proyecto Unity | Cowork, conectores, elección de modelos |
| **M01** | Autómatas celulares | Game of Life (Conway), emergencia, variantes (Brian's Brain, hormiga de Langton) | State machine, object pooling, separación simulación/render | Claude Code + primer ciclo SDD completo |
| **M02** | Enjambres | Boids (Reynolds), steering behaviors, estigmergia (hormigas) | Strategy, spatial partitioning, ECS/Jobs básico | Subagentes para tareas paralelas |
| **M03** | Números complejos y fractales de escape | Plano complejo, Mandelbrot, Julia, coloreado | Compute shaders, cámara/zoom infinito | Skill propia (crear contrato) |
| **M04** | Fractales geométricos | L-systems, IFS, dimensión fractal, árboles y costas | Builder, recursión controlada, mesh procedural | Conector Jira a pleno uso |
| **M05** | Teoría del caos | Mapa logístico, atractor de Lorenz, doble péndulo, sensibilidad a condiciones iniciales | Observer, trail rendering, fixed timestep | Automatizaciones/tareas programadas |
| **M06** | Vida artificial avanzada | Ecosistemas, algoritmos genéticos, Lenia, autorreplicación | ECS/DOTS, ScriptableObjects para genomas | Agentes con roles (implementador/revisor) |
| **M07** | Origen de la vida | Química artificial, autocatálisis, autopoiesis | Simulación de partículas, compute shaders avanzados | Pipeline completo multi-herramienta |
| **M08** | Consciencia y cognición | Agentes con percepción-acción, redes simples, teorías (IIT, GWT) — fuerte componente de dossier | Behavior trees, sensores | Investigación profunda + publicación |

Notas:

- M01–M03 son cortos y muy visuales: sirven para asentar el método antes de subir la complejidad.
- Blender entra de forma natural en M06+ (criaturas, entornos); antes no hace falta.
- Los dossiers acompañan a los módulos (p. ej. dossier "Emergencia" en M01, "Teorías de la consciencia" en M08), pero pueden pedirse en cualquier momento.
- Candidatos naturales a publicación: M03 (fractales, muy vistoso), M05 (caos) y M08 (consciencia, ensayo).
