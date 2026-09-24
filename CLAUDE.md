# CLAUDE.md — Qatu

Proyecto: **Qatu**, marketplace de alquiler de herramientas/equipos y de servicios de oficios
(pintor, jardinero, gasfitero, electricista, carpintero, cerrajero, limpieza, etc.).
Ciudad piloto: Ayacucho, Perú. Moneda PEN. Zona horaria America/Lima. Idioma de UI: es-PE.

## Metodología: Spec-Driven Development con GitHub Spec Kit
- La constitución vive en `.specify/memory/constitution.md`. Es ley: si una tarea la contradice, detente y avisa.
- Cada feature vive en `specs/NNN-nombre/` con `spec.md`, `plan.md`, `tasks.md` (+ `data-model.md`, `contracts/`, `research.md`).
- Flujo: `/speckit.specify` → `/speckit.clarify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.analyze` → `/speckit.implement`.
- Fuente de verdad del negocio: `docs/01-producto.md` … `docs/05-confianza-y-legal.md`. Léelos antes de especificar o planificar.
- Nunca inventes reglas de negocio: si algo no está en `docs/` ni en el spec, márcalo `[NEEDS CLARIFICATION]`.

## Definición de terminado (toda tarea)
1. Tests pasan (unitarios de dominio + integración de API con Postgres real vía Testcontainers).
2. Lint y typecheck limpios (`pnpm lint && pnpm typecheck`).
3. Migraciones de BD incluidas y reversibles.
4. Contrato OpenAPI actualizado si cambió la API.
5. Toda transición de estado pasa por la máquina de estados del dominio y queda en `audit_log`.
6. Montos en céntimos (enteros). Nunca `float` para dinero.
7. Sin secretos en el código. Variables en `.env.example`.

## Comandos
- `pnpm dev` — levanta web + api + worker (requiere `docker compose up -d` para Postgres/Redis/MinIO)
- `pnpm test` · `pnpm test:e2e` · `pnpm lint` · `pnpm typecheck`
- `pnpm db:migrate` · `pnpm db:seed`

## Convenciones
- Código y nombres técnicos en inglés; textos de UI, specs y docs en español.
- Commits: Conventional Commits (`feat(rentals): ...`).
- Un módulo no importa internals de otro: solo su API pública (`index.ts`) o eventos de dominio.
