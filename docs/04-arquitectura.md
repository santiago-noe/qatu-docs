# 04 — Arquitectura

## Principio
**Monolito modular** primero, listo para extraer servicios después. Un solo deploy, una BD, límites
de módulo estrictos. Se extrae un módulo a servicio solo con métrica que lo justifique
(ej. `search` o `notifications` por carga).

## Stack
| Capa | Elección | Por qué |
|---|---|---|
| Monorepo | pnpm workspaces + Turborepo | Compartir tipos y esquemas entre web/api/mobile |
| Lenguaje | TypeScript estricto en todo | Un solo lenguaje, tipos compartidos |
| API | NestJS (REST, OpenAPI generado) | Módulos, DI, guards; encaja con monolito modular |
| Validación | Zod (paquete `@qatu/contracts`) | Mismo esquema en front y back |
| ORM / migraciones | Drizzle ORM + drizzle-kit (o Prisma si se prefiere) | SQL explícito, migraciones versionadas |
| BD | PostgreSQL 16 + **PostGIS** + `btree_gist` (+ `pgvector` en fase IA) | Geo, rangos de fechas, vectores en una sola BD |
| Caché / colas | Redis + **BullMQ** | Timeouts, recordatorios, reintentos, webhooks |
| Archivos | S3-compatible (Cloudflare R2 / MinIO en local) con URLs prefirmadas | Subida directa desde el cliente, sin pasar por la API |
| Imágenes | Redimensión en worker (sharp) → WebP, varias resoluciones | Datos móviles caros |
| Web | Next.js (App Router) + Tailwind + shadcn/ui, PWA | SEO para anuncios, rápido en móvil |
| Móvil (fase 2) | Expo / React Native reutilizando `@qatu/contracts` y cliente API | Misma API |
| Auth | OTP por SMS/WhatsApp (principal) + email opcional; JWT de acceso corto + refresh rotativo en cookie httpOnly | En Perú el teléfono es la identidad práctica |
| Tiempo real | WebSockets (Socket.IO con adaptador Redis) para chat y estados | Escala horizontal |
| Notificaciones | Push web (FCM), WhatsApp Business API / SMS, email | Canal por preferencia del usuario |
| Mapas | MapLibre + tiles OSM; geocodificación con Nominatim o proveedor comercial | Sin costo inicial |
| Búsqueda | Postgres FTS (`unaccent`, config spanish) + PostGIS; OpenSearch solo si hace falta | Menos piezas al inicio |
| Observabilidad | OpenTelemetry → Grafana/Tempo/Loki (o Sentry para errores) | Trazas por transacción |
| Contenedores | Docker + docker compose (local); imagen única api/worker | Portable a cualquier nube |
| CI/CD | GitHub Actions (lint, typecheck, test, build, migraciones) | Integrado con Spec Kit/issues |
| Tests | Vitest (unit), Supertest + Testcontainers (integración), Playwright (e2e) | BD real en tests |

## Estructura del repo
```
qatu/
├─ apps/
│  ├─ api/            # NestJS: src/modules/<modulo>/{domain,application,infrastructure,http}
│  ├─ worker/         # procesadores BullMQ (mismo código de módulos, otro entrypoint)
│  └─ web/            # Next.js
├─ packages/
│  ├─ contracts/      # esquemas Zod, tipos, enums de estados, OpenAPI
│  ├─ ui/             # componentes compartidos
│  └─ config/         # eslint, tsconfig
├─ infra/             # docker-compose, Dockerfiles, IaC futura
├─ docs/              # contexto de negocio (este paquete)
├─ specs/             # generado por Spec Kit
└─ .specify/
```
Capas por módulo: `domain` (entidades, máquinas de estado, reglas puras, sin dependencias de framework)
→ `application` (casos de uso) → `infrastructure` (repos, adaptadores PSP, S3) → `http` (controllers, DTOs).

## Patrones obligatorios para escalar
1. **Sin doble reserva**: `AvailabilityBlock` con `EXCLUDE USING gist (listing_id WITH =, period WITH &&)`
   — la BD garantiza que dos reservas/holds no se solapen, aunque haya concurrencia.
2. **Idempotencia**: header `Idempotency-Key` en endpoints de creación y pago; webhooks del PSP
   deduplicados por `provider_event_id`.
3. **Outbox transaccional**: eventos de dominio (`RentalConfirmed`, `JobCompleted`…) se escriben en la
   misma transacción y un relay los publica a BullMQ. Nada de "guardar y luego enviar" sin garantía.
4. **Jobs programados por entidad**: al crear una solicitud se agenda su expiración; el job verifica
   el estado antes de actuar (idempotente).
5. **Ledger inmutable** para dinero (ver docs/03).
6. **Multi-ciudad desde el día 1**: `city_id` en listings, providers, requests, settings de comisión y
   políticas. Ayacucho es solo la primera fila.
7. **Configuración en BD** (`platform_settings`, por ciudad/categoría): comisiones, timeouts, límites.
8. **API stateless** detrás de balanceador; sesión en JWT/Redis; archivos en S3 → escala horizontal.
9. **Paginación por cursor**, índices GIST/GIN, `EXPLAIN` en consultas de búsqueda.
10. **Feature flags** (tabla simple) para lanzar por ciudad o por grupo.
11. **Rate limiting** por IP y usuario (Redis) en OTP, búsqueda, mensajes y creación de solicitudes.
12. **Versionado de API** `/v1`.

## Seguridad
- RBAC (roles) + verificación de propiedad en cada caso de uso (un arrendador solo ve sus reservas).
- Datos sensibles (DNI, selfies, antecedentes, dirección exacta) en bucket privado, cifrado en reposo,
  acceso por URL prefirmada de corta duración y registrado en `audit_log`.
- Dirección exacta y teléfono solo visibles tras confirmación de la transacción.
- Códigos de entrega/inicio: se guardan hasheados, expiran, con límite de intentos.
- OWASP ASVS nivel 2 como checklist. Headers de seguridad, CSP, CSRF en cookies.
- Backups diarios de Postgres con prueba de restauración mensual.

## Despliegue sugerido por etapa
| Etapa | Infra |
|---|---|
| Local | docker compose: postgres(postgis), redis, minio, mailpit |
| Piloto (bajo costo) | 1 VPS o PaaS (Railway/Render/Fly) para api+worker+web, Postgres gestionado con PostGIS, R2 |
| Crecimiento | Contenedores en nube (réplicas de api y worker), Postgres con réplica de lectura, CDN |
