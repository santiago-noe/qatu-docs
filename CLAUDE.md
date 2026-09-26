# CLAUDE.md — Qatu

Proyecto: **Qatu**, marketplace de alquiler de herramientas/equipos y de servicios de oficios
(pintor, jardinero, gasfitero, electricista, carpintero, cerrajero, limpieza, etc.).
Ciudad piloto: Ayacucho, Perú. Moneda PEN. Zona horaria America/Lima. Idioma de UI: es-PE.
La visión es un ecosistema local por fases (docs/01): el piloto cubre alquiler y servicios; productos y espacios vienen después.

## Repos (3 independientes, sin monorepo ni submodules)
| Repo | Contenido | Stack |
|---|---|---|
| `qatu-api` | Backend | Go 1.26+, arquitectura hexagonal, Fiber v3, pgx v5 (PostgreSQL 18 + PostGIS), Redis 8, zerolog, viper |
| `qatu-app` | Frontend | Next.js 16 (App Router), React 19, TypeScript, Tailwind v4, shadcn/ui, lucide-react, recharts, bun |
| `qatu-docs` | Este repo: documentación, prompts de Spec Kit, colecciones Bruno, trazabilidad de requerimientos | Markdown |

Detalle del stack y de la estructura de carpetas: `docs/04-arquitectura.md`.

## Metodología: Spec-Driven Development con GitHub Spec Kit
- La constitución vive en `.specify/memory/constitution.md` (se genera desde `prompts/00-constitution.md`). Es ley: si una tarea la contradice, detente y avisa.
- Cada feature vive en `specs/NNN-nombre/` con `spec.md`, `plan.md`, `tasks.md` (+ `data-model.md`, `contracts/`, `research.md`).
- Flujo: `/speckit.specify` → `/speckit.clarify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.analyze` → `/speckit.implement`.
- En cada `/speckit.plan` se pega `prompts/plan-base.md` y el bloque "PLAN (extra)" de la feature.
- Fuente de verdad del negocio: `docs/01-producto.md` … `docs/05-confianza-y-legal.md`. Léelos antes de especificar o planificar.
- Nunca inventes reglas de negocio: si algo no está en `docs/` ni en el spec, márcalo `[NEEDS CLARIFICATION]`.

## Definición de terminado (toda tarea)
1. Tests pasan: `qatu-api` con `go test` (unitarios de dominio + integración con Postgres y Redis reales vía Testcontainers); `qatu-app` con bun test y Playwright para los flujos críticos.
2. Lint y typecheck limpios en el repo que se toca (ver comandos).
3. Migraciones de BD incluidas y reversibles (`.up.sql` y `.down.sql`, numeradas).
4. Contrato OpenAPI y colecciones Bruno actualizados si cambió la API.
5. Toda transición de estado pasa por la máquina de estados del dominio y queda en `audit_log`.
6. Montos en céntimos (enteros). Nunca `float` para dinero.
7. Sin secretos en el código. Variables en `.env.example` (prefijo `APP__` en la API).
8. PostgreSQL es la fuente de verdad: Redis solo cachea, coordina y limita; no decide disponibilidad, precios ni dinero.
9. La ubicación exacta y el teléfono nunca salen en respuestas públicas; el mapa usa la ubicación aproximada.
10. La sección "Estado de implementación" de los requerimientos en `qatu-docs` se actualiza (✅ ⚠️ ❌ 🔮).

## Comandos

### qatu-api
- `docker compose up -d` — Postgres y Redis (requiere la red externa `svc`: `docker network create svc`)
- `make dev` — live-reload con air · `make run` — servidor sin live-reload
- `make test` · `make lint` · `make build`
- `make migrate-up` · `make migrate-down`

### qatu-app
- `bun install` · `bun run dev` — desarrollo
- `bun run typecheck` · `bun run build`
- `bun run start` — producción: `entrypoint.sh` levanta Next en el puerto interno y `gateway.js` en el público
- Pendiente de configurar: ESLint o Biome y las pruebas (bun test, Playwright, Lighthouse CI).

## Convenciones
- Código y nombres técnicos en inglés; textos de UI, specs y docs en español.
- Commits: Conventional Commits (`feat(rentals): ...`, `docs(landing): ...`). Sin línea de coautoría de Claude.
- Un módulo no importa internals de otro: solo su servicio público o eventos de dominio.
- En `qatu-app`, cada feature es autocontenida en `features/{public,protected,auth}/<feature>/{components,lib}`; el navegador llama solo al BFF (`app/api`), nunca al backend directo.
- En `qatu-api`, el dominio (`internal/core/domain`) no importa infraestructura; los adaptadores implementan los `port`.
