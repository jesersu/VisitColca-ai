# Ruta y decisiones

Mapa vivo del proyecto: dónde estamos, qué se decidió y por qué.
Se actualiza **cuando una decisión se cierra**, no al final.

Última actualización: 2026-09-09

---

## Estado actual

**Fase 1 — Conceptos de IA.** La skill está hecha; falta el agente.

> No hay código Swift todavía. Las fases 0 y 1 son cimientos, y el desvío hacia
> convenciones de git fue deliberado, pero conviene tenerlo presente.

---

## La ruta

| Fase | Qué se construye | Material de apoyo | Estado |
|:--:|---|---|:--:|
| 0 | Cimientos: documentos, repo, monorepo, convenciones | AI Fluency: Framework & Foundations | ✅ |
| 1 | Una skill y un agente, para ver la diferencia en carne propia | Introduction to Agent Skills · Introduction to Subagents | 🔄 |
| 2 | Dominio de turismo en Swift puro, con TDD | — | ⬜ |
| 3 | Catálogo en SwiftUI sobre el dominio ya testeado | — | ⬜ |
| 4 | Backend FastAPI con SDD completo | Claude Code in Action | ⬜ |
| 5 | Orquestador de agentes | Introduction to MCP · tool_use | ⬜ |

Las fases 2 y 3 **no tienen material de IA a propósito**: ahí se aprende Swift, no
prompting. Mezclar las dos cosas impide saber cuál de las dos falló.

---

## Decisiones

### Producto y arquitectura

| Decisión | Por qué |
|---|---|
| El proyecto es vehículo de aprendizaje de IA **y** app real de turismo | Aprender haciendo, sobre un dominio con reglas de negocio de verdad |
| Orden de clientes: **iOS → Android → web** | Decisión del usuario; el web es el último, no el primero |
| Apps **nativas** por plataforma, no React Native / Flutter / KMP | El objetivo incluye aprender cada stack, no abstraerlo |
| Un solo backend FastAPI, **client-agnostic** | Tres clientes sobre una API: ningún endpoint a medida de una pantalla |
| El SEO se **acotó** al cliente web, no se eliminó | Las apps nativas no son canal de indexación, pero la web sigue siendo obligatoria |
| El panel de admin vive en el cliente web | Llega en la fase 3; las apps móviles no llevan admin |

### Repositorio

| Decisión | Por qué |
|---|---|
| **Monorepo** | El contrato de la API es el artefacto compartido crítico: cambiar backend y adaptar cliente debe ser un commit atómico |
| Desventajas asumidas | Conflictos de merge en `project.pbxproj` (mitigar con SPM + Tuist/XcodeGen) y costo de runners macOS en CI (mitigar con `paths:` filters) |
| Repo `VisitColca-ai` | El nombre original `VisitColca-ios` mentía: nombraba un cliente conteniendo cuatro |

### Git

| Decisión | Por qué |
|---|---|
| **Trunk-based, no Gitflow** | El propio autor de Gitflow lo desaconseja para entrega continua; `develop` con un solo dev es ceremonia sin información |
| Ramas en minúsculas | El filesystem de macOS es case-insensitive: `FE/x` y `fe/x` colisionan |
| El tipo de rama usa el vocabulario de los commits | Un solo término en label, issue, rama y commit — sin traducción mental |
| **Issue obligatorio en `feat` y `fix`**, opcional en `chore` y `docs` | Los primeros cambian comportamiento y el diff no explica el porqué meses después; los segundos sí se explican solos |
| Número de issue de GitHub, nunca un contador manual | Un contador a mano se desincroniza; GitHub asigna y enlaza gratis |
| Squash merge y borrar la rama | `main` se lee como changelog; la rama es un puntero de 41 bytes sin historia propia |
| Conventional commits en inglés, sin atribución de IA | Convención inglesa; la regla de atribución es del usuario |
| Tags prefijados por entregable (`ios/v1.0.0`) | Cada entregable versiona a su propio ritmo; un `v1.0.0` pelado no significa nada |
| Release branches solo cuando hay algo que congelar | Antes de eso son un `develop` disfrazado; Git permite ramificar desde cualquier commit del historial |

### GitHub

| Decisión | Por qué |
|---|---|
| Labels en dos ejes: `type:` y `scope:` | Con un solo eje harían falta 25 labels y no se podría filtrar "todo lo de iOS" |
| `type:` con color semántico, `scope:` en gris uniforme | El color transporta significado; `ios` no es "mejor" que `backend` |
| Borrados `bug`, `documentation`, `enhancement` | Competían con el vocabulario: `bug` y `fix` son lo mismo con dos nombres |
| **NO** versionar los labels (por ahora) | Principio 5 de la constitución: no hay problema concreto identificado, y postergarlo cuesta lo mismo |

### IA

| Decisión | Por qué |
|---|---|
| Primera skill: `constitution-guard` | `CLAUDE.md` se autocarga, `specs/constitution.md` no — ahí estaba el agujero real |
| Una skill no copia un documento | El documento dice qué creemos; la skill dice qué hacer cuando alguien está por romperlo |

---

## Deuda anotada

Cosas postergadas a conciencia, con el criterio para retomarlas.

| Deuda | Cuándo retomarla |
|---|---|
| `.github/labels.yml` + workflow que los aplique | Cuando entre otra persona, o al tercer cambio manual de labels |
| Sección `## Comandos` de `CLAUDE.md`, vacía | Al inicializar el proyecto de Xcode |
| Path filters en la CI | Con el primer workflow |

---

## Referencias

Material oficial verificado el 2026-09-07:

- [Claude Academy](https://academy.claude.com/) — 21 cursos gratuitos
- [anthropics/courses](https://github.com/anthropics/courses) — 5 cursos hands-on en notebooks
- [Claude Certified Architect – Foundations](https://anthropic-partners.skilljar.com/claude-certified-architect-foundations-certification) — USD 125; cubre Claude Code, Agent SDK, Claude API y MCP. Vive en el Partner Academy, no en el catálogo público

> Los sitios de terceros que venden "exam prep" para esta certificación no están
> respaldados por Anthropic. La guía oficial del examen está en el enlace de arriba.
