# 04 — Arquitectura

> Revisión 2026-09-26. Reemplaza el stack TypeScript/NestJS por Go + Redis y se alinea con la
> propuesta v2.0 (`Qatu_Propuesta_Arquitectura.docx`) conservando su visión y ajustando lo que no escala.

## Principio
**Monolito modular** primero, listo para extraer servicios después. Un solo deploy, una BD, límites
de módulo estrictos. Se extrae un módulo a servicio solo con métrica que lo justifique
(ej. `search` o `notifications` por carga). Esto coincide con la propuesta v2.0 (sección 11).

## Decisiones frente a la propuesta v2.0
| Tema | Propuesta v2.0 | Decisión | Por qué |
|---|---|---|---|
| Visión | Comercio + alquileres + servicios | **Se mantiene**, por fases | Es el diferenciador de Qatu |
| Alcance del MVP | Productos, inmuebles, vehículos, espacios, servicios e IA a la vez | MVP = alquiler de herramientas/equipos + servicios de oficios | Liquidez local antes que amplitud; la propia propuesta pide categorías específicas |
| Productos, espacios, equipos tecnológicos | En el MVP | Fase 2–3, activados por `vertical` en categorías | El modelo lo soporta sin reescribir |
| Inmuebles y vehículos | En el MVP | Fase 4, solo con revisión legal previa | Contratos de arrendamiento, seguros, riesgo alto |
| Backend | NestJS o FastAPI | **Go** (hexagonal, Fiber v3) | Concurrencia, bajo consumo, un binario; el patrón lister-core ya lo define |
| Caché y colas | Redis | **Redis** con reglas explícitas (abajo) | Sesiones, caché, rate limit, bloqueos y jobs |
| Web | Next.js + React + TypeScript | **Se mantiene** (Next.js 16) | Sin cambios |
| Móvil | React Native o Flutter | Fase 2: PWA primero; React Native/Expo si la tracción lo justifica | Costo de mantener dos clientes al inicio |
| Auth | JWT / OAuth 2.0 | Sesión en cookie httpOnly + store en Redis. Piloto: correo y contraseña + Google; después, OTP por celular como un proveedor más | Revocable, sin JWT stateless que no se puede invalidar; empezar simple sin cerrar la puerta al celular |
| CI/CD | GitLab CI/CD | GitHub Actions | Los repos viven en GitHub |
| Mapas | OSM / Mapbox / Google Maps | MapLibre + tiles OSM (proveedor de tiles en producción) | Sin costo inicial, sin vendor lock-in |
| IA | Búsqueda semántica, asistente y generación en el MVP | Post-MVP (features 018 y 019) | Primero datos reales; la IA nunca inventa precios ni disponibilidad |

## Stack
| Capa | Elección | Por qué |
|---|---|---|
| Repos | 3 repos independientes: `qatu-api`, `qatu-app`, `qatu-docs` | Ciclos de despliegue separados; sin monorepo ni submodules |
| Lenguaje API | Go 1.26+, arquitectura hexagonal (`domain` / `port` / `service` / `adapter`) | Dominio sin dependencias de framework |
| HTTP | Fiber v3, rutas versionadas `/api/v1` | Ligero y rápido |
| BD | PostgreSQL 18 + **PostGIS** + `btree_gist` (+ `pgvector` en fase IA) vía pgx v5 | Geo, rangos de fechas y vectores en una sola BD |
| Migraciones | SQL plano numerado (`migrations/NNNN_*.up.sql` / `.down.sql`) | Reversibles y auditables |
| Caché / sesiones / colas | Redis 8 (go-redis v9) + asynq para jobs | Ver sección Redis |
| Archivos | S3-compatible (MinIO local, Cloudflare R2 en producción) con URLs prefirmadas | Subida directa desde el cliente |
| Imágenes | Redimensión en worker → WebP en varias resoluciones | Datos móviles caros |
| Web | Next.js 16 (App Router), React 19, Tailwind v4, shadcn/ui, feature-sliced, bun | Ya definido en `qatu-app` |
| BFF | Rutas `app/api` de Next como proxy server-side hacia Go | El navegador nunca llama al backend directo |
| Gateway | `gateway.js`: un solo puerto público; el WebSocket va directo a Go | Realtime sin exponer dos puertos |
| Auth | Piloto: correo y contraseña (argon2id) + Google (OAuth 2.0 con PKCE). Después: OTP por SMS/WhatsApp (feature 022). Sesión en cookie httpOnly + Redis; `require_role` | Sin costo de SMS al inicio; el celular se suma sin rehacer nada (ver "Acceso extensible") |
| Tiempo real | WebSocket nativo en Go con Redis pub/sub entre réplicas | Escala horizontal |
| Notificaciones | Push web, WhatsApp Business / SMS, email | Canal por preferencia |
| Mapas | MapLibre + tiles OSM; geocodificación con Nominatim o proveedor comercial | Sin costo inicial |
| Búsqueda | Postgres FTS (`unaccent`, `spanish`) + PostGIS; OpenSearch solo si hace falta | Menos piezas al inicio |
| Observabilidad | zerolog + OpenTelemetry → Grafana/Tempo/Loki (o Sentry) | Trazas por transacción |
| Contenedores | Docker + compose (`db`, `redis`, red externa `svc`) | Portable |
| CI/CD | GitHub Actions por repo | Lint, tests, build, migraciones |
| Tests | `go test` + Testcontainers; bun test y Playwright en la app | BD real en tests |

## Estructura de repos
```
qatu-api/                     # Go, hexagonal
├─ cmd/{server,migrations}
├─ internal/core/{domain,port,service}
├─ internal/adapter/inbound/http/{handler,middleware,router.go}
├─ internal/adapter/outbound/{postgres,redisclient,smtp,googleoauth,argon2,turnstile,ipquery}
├─ internal/adapter/{logging}
├─ internal/config
└─ migrations/
qatu-app/                     # Next.js 16
├─ app/{(public),(protected)/dashboard,auth,api,unauthorized}
├─ features/{public,protected,auth}/<feature>/{components,lib}
├─ components/{layout,provider,ui}   proxy.ts   gateway.js
qatu-docs/                    # este repo: docs, prompts Spec Kit, colecciones Bruno, requisitos
```
Un módulo de dominio no importa internals de otro: se comunican por su servicio público o por eventos
de dominio. Cada módulo tiene su `domain` (entidades, máquinas de estado, reglas puras), sus `port`
(interfaces) y su `service` (casos de uso); los adaptadores implementan los ports.

## Acceso extensible (cuentas e identidades)
El piloto entra con correo y contraseña o con Google, y el OTP por celular llega después. Para que ese cambio no obligue a rehacer nada:
- **La cuenta y el acceso están separados.** `users` guarda a la persona; `auth_identities` guarda cada forma de entrar (`password`, `google` y, luego, `phone_otp`), con `UNIQUE (provider, provider_subject)`.
- **Un contrato por proveedor.** `port.AuthProvider` con implementaciones para contraseña, Google y OTP; todas terminan en el mismo `SessionService`.
- **Sesiones independientes del proveedor.** Redis guarda el usuario y sus roles; el proveedor usado es solo un dato de auditoría.
- `users` ya reserva `phone` y `phone_verified_at` (nulos en el piloto) y exige correo o celular, de modo que una cuenta solo con celular será válida.
- Una prueba de extensibilidad con un proveedor falso protege esta separación (spec 001).

## Redis: qué se guarda y qué no
Redis acelera y coordina; **la fuente de verdad siempre es PostgreSQL**.

| Uso | Detalle | TTL / política |
|---|---|---|
| Sesiones | `session:{id}` → usuario, roles, expiración | Igual a la sesión; revocación inmediata al cerrar sesión |
| Códigos | Verificación de correo, recuperación de contraseña, 2FA, códigos de entrega y, después, OTP | Minutos; máximo de intentos; los de entrega se guardan hasheados |
| Rate limiting | Por IP y por usuario (inicio de sesión, envío de códigos, búsqueda, mensajes, solicitudes) | Ventana deslizante |
| Idempotencia | `Idempotency-Key` de creación y pago; segunda barrera en BD | 24 h |
| Caché de lectura | Categorías, ciudades y zonas, `platform_settings`, ficha pública de publicación, resultados de búsqueda | 60 s a 1 h según dato; **cache-aside**; invalidación al escribir |
| Anti-estampida | `singleflight` en Go + TTL con jitter | Evita que una clave caliente golpee la BD |
| Bloqueo de fechas | Hold temporal mientras el arrendador responde o el cliente paga | Acelera la UX; la garantía real es la restricción de exclusión en Postgres |
| Pub/Sub | Eventos de chat y de estado hacia el WebSocket entre réplicas | Sin persistencia |
| Jobs | asynq: timeouts de solicitud, recordatorios T-24 h y T-2 h, liquidaciones, reintentos | Reintentos con backoff; idempotentes |

**No se cachea:** disponibilidad como verdad, precios de una transacción (se guardan en el snapshot), saldos
ni asientos del ledger, permisos más allá de los roles de la sesión, y datos sensibles (DNI, dirección exacta).
La dirección exacta y el teléfono solo se sirven tras confirmar la transacción y nunca pasan por la caché pública.

## Patrones obligatorios para escalar
1. **Sin doble reserva**: `AvailabilityBlock` con `EXCLUDE USING gist (listing_id WITH =, period WITH &&)`.
   La BD lo garantiza aunque haya concurrencia; Redis solo agrega un hold rápido.
2. **Idempotencia**: `Idempotency-Key` en endpoints de creación y pago; webhooks del PSP deduplicados por `provider_event_id`.
3. **Outbox transaccional**: los eventos de dominio (`RentalConfirmed`, `JobCompleted`…) se escriben en la misma
   transacción y un relay los publica a asynq. Nada de "guardar y luego enviar" sin garantía.
4. **Jobs programados por entidad**: al crear una solicitud se agenda su expiración; el job verifica el estado antes de actuar.
5. **Ledger inmutable** para dinero (ver docs/03).
6. **Multi-ciudad desde el día 1**: `city_id` en publicaciones, proveedores, solicitudes, comisiones y políticas. Ayacucho es la primera fila.
7. **Configuración en BD** (`platform_settings`, por ciudad y categoría): comisiones, timeouts, límites; cacheada en Redis.
8. **API stateless** detrás de balanceador: sesión en Redis, archivos en S3. Réplicas de la API sin estado local.
9. **Paginación por cursor**, índices GIST/GIN, `EXPLAIN` en las consultas de búsqueda; `pgbouncer` y réplica de lectura al crecer.
10. **Feature flags** (tabla simple) para lanzar por ciudad o por grupo.
11. **Rate limiting** por IP y usuario en Redis.
12. **Versionado de API** `/api/v1`; los cambios que rompen contrato requieren nueva versión.
13. **Vertical como dato**: `category.vertical` (RENTAL, SERVICE y, en fases futuras, PRODUCT, SPACE) permite activar
    verticales sin reescribir el núcleo.

## Detalle de publicación con mapa (estilo Airbnb)
Al abrir una publicación el usuario ve la ficha completa y, muy visible, **dónde se encuentra**:
- Galería de fotos, precio calculado para las fechas, garantía, política de cancelación y reputación del oferente.
- **Mapa con ubicación aproximada** (círculo o zona, no el punto exacto) con MapLibre; el oferente y la ficha muestran el barrio o distrito.
- Distancia aproximada desde el usuario y, en la búsqueda, resultados en lista y en mapa con pines.
- **La dirección exacta y el teléfono se revelan solo tras confirmar la transacción** (docs/05).
- Técnica: PostGIS `ST_DWithin` con índice GIST; las coordenadas públicas se ofuscan (redondeo o desplazamiento aleatorio dentro de un radio fijo) y se guardan aparte de la ubicación real.
- Rendimiento: la ficha pública se cachea en Redis con invalidación al editar; el mapa carga de forma diferida para cumplir el presupuesto de JS inicial.

## Seguridad
- RBAC (roles) + verificación de propiedad en cada caso de uso (un arrendador solo ve sus reservas).
- Datos sensibles (DNI, selfies, antecedentes, dirección exacta) en bucket privado, cifrado en reposo,
  acceso por URL prefirmada de corta duración y registrado en `audit_log`.
- Contraseñas con argon2id; sesiones en cookie httpOnly, Secure y SameSite; CSRF en el BFF.
- Códigos de entrega/inicio: hasheados, con expiración y límite de intentos.
- Captcha Turnstile en formularios públicos; rate limiting en Redis.
- OWASP ASVS nivel 2 como checklist. Headers de seguridad y CSP.
- Backups diarios de Postgres con prueba de restauración mensual.

## Despliegue sugerido por etapa
| Etapa | Infra |
|---|---|
| Local | docker compose: postgres (PostGIS), redis, minio, mailpit; la app con `bun run dev` |
| Piloto (bajo costo) | 1 VPS o PaaS (Railway, Render, Fly) para api + app (gateway), Postgres gestionado con PostGIS, Redis gestionado, R2 |
| Crecimiento | Réplicas de la API y del worker, Postgres con réplica de lectura y pgbouncer, Redis gestionado, CDN |

## Riesgos y decisiones abiertas
- **Cola de jobs:** se elige asynq (sobre Redis). Si se prefiere que los jobs sean transaccionales con Postgres, se evalúa `river`; se decide en `research.md` de la feature 010.
- **Tiles de mapa en producción:** el servidor público de OSM no admite tráfico comercial; se necesita un proveedor de tiles.
- **OTP (feature 022):** canal (SMS, WhatsApp o ambos) y proveedor por definir; se agrega como adaptador `OtpSender` y un nuevo proveedor de acceso, sin cambios en usuarios ni sesiones.
- **Pagos:** el MVP usa registro manual, sin custodia; las pasarelas entran detrás de `PaymentGateway`.
