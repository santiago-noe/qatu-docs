<!-- Actualizado 2026-09-26: stack Go + Next.js en 3 repos, Redis, visión por fases y mapa con ubicación aproximada. -->
/speckit.constitution Crea la constitución de Qatu, un marketplace de alquiler de herramientas y servicios de oficios para Ayacucho (Perú) preparado para expandirse a otras ciudades y, por fases, a productos y espacios (docs/01). Lee primero docs/01-producto.md a docs/05-confianza-y-legal.md. Principios no negociables:

I. Especificación primero. Ninguna funcionalidad se implementa sin spec aprobado. Las reglas de negocio salen de docs/ o del spec; si faltan, se marcan [NEEDS CLARIFICATION], nunca se inventan.

II. Dominio en el centro. Arquitectura hexagonal en Go (qatu-api): internal/core/{domain,port,service} sin dependencias de framework ni de infraestructura, y adaptadores en internal/adapter. Cada módulo (identity, catalog, listings, providers, search, rentals, service-jobs, payments, deposits, messaging, notifications, reviews, disputes, admin) solo se comunica con otro por su servicio público o por eventos de dominio vía outbox. Tres repos independientes (qatu-api, qatu-app, qatu-docs), sin monorepo ni submodules. Monolito modular; nada de microservicios sin justificación medida. Los verticales futuros (productos, espacios) se activan por category.vertical sin tocar el núcleo.

III. Estados explícitos. Toda entidad transaccional (reserva, trabajo, cotización, garantía, pago, disputa) cambia de estado solo mediante su máquina de estados, con validación de actor y guardas, registro en audit_log y evento en outbox en la misma transacción de BD.

IV. Dinero correcto. Montos en céntimos enteros, moneda explícita (PEN). Ledger de doble partida inmutable. Snapshot inmutable del desglose de precio en cada transacción. Nunca comisión sobre garantías ni sobre reembolso de materiales. Todo endpoint que cree transacciones o pagos es idempotente. Qatu no custodia fondos de terceros: los pagos pasan por la interfaz PaymentGateway (adaptadores Manual, MercadoPagoSplit, Culqi).

V. Integridad bajo concurrencia. Disponibilidad protegida por restricción de exclusión en PostgreSQL (sin doble reserva). Bloqueo optimista con columna version. Jobs de timeout idempotentes. PostgreSQL es la fuente de verdad: Redis acelera y coordina (sesiones, caché, rate limiting, holds, jobs) pero nunca decide disponibilidad, precios de una transacción, dinero ni permisos.

VI. Test-first en el dominio. Tests unitarios (go test, tablas de casos) obligatorios para máquinas de estado, cálculo de precios, comisiones y reglas de garantía; tests de integración contra Postgres y Redis reales (Testcontainers) para cada caso de uso; en qatu-app, bun test para lib/ y componentes y e2e con Playwright (más axe para accesibilidad) para los flujos críticos: reservar herramienta, contratar servicio, abrir disputa. Cobertura mínima de dominio 90%.

VII. Contratos primero. La API expone un contrato OpenAPI versionado (/api/v1) que es la fuente de verdad entre qatu-api y qatu-app; los tipos del frontend se derivan de él y las colecciones Bruno de qatu-docs se mantienen al día. El navegador solo habla con el BFF (app/api de Next), nunca con el backend directo. Cambios que rompen contrato requieren nueva versión.

VIII. Privacidad por diseño (Ley 29733 y D.S. 016-2024-JUS). Minimización de datos, consentimiento por finalidad, consentimiento expreso para biometría, archivos sensibles en bucket privado con URLs prefirmadas de corta duración, dirección exacta y teléfono visibles solo tras confirmar la transacción, accesos a datos sensibles auditados. El mapa público muestra solo una ubicación aproximada (ofuscada de forma determinista) y el distrito; el punto exacto nunca se cachea ni se publica.

IX. Seguridad. RBAC (middleware require_role) + verificación de propiedad en cada caso de uso, sesión en cookie httpOnly con store en Redis (revocable; sin JWT stateless), rate limiting en Redis, OTP con límite de intentos, contraseñas con argon2id, códigos de entrega hasheados, captcha en formularios públicos, OWASP ASVS nivel 2, sin secretos en el repo.

X. Escalable por configuración. city_id en todas las entidades con ubicación; comisiones, timeouts y políticas en platform_settings por ciudad/categoría; feature flags para lanzamientos graduales; API stateless.

XI. Móvil y conexión lenta primero. Web PWA responsive, presupuesto de rendimiento (LCP < 2,5 s en 4G lento, JS inicial < 200 KB, mapas y contenido pesado con carga diferida), imágenes WebP redimensionadas, UI en español claro (es-PE) y accesible (WCAG 2.1 AA).

XII. Transparencia al usuario. El total (alquiler/servicio + tarifa + delivery + garantía) se muestra desglosado antes de confirmar. La IA nunca inventa precios, disponibilidad ni condiciones y se identifica como IA.

XIII. Simplicidad. YAGNI; la dependencia nueva debe justificarse en research.md. Preferir Postgres (FTS, PostGIS, pgvector) antes que añadir otro motor; Redis es la única pieza adicional de estado. Se abre un vertical o una fase de docs/01 solo cuando la anterior está validada.

Gobernanza: la constitución prevalece sobre specs y planes; enmiendas con versión semántica y fecha; /speckit.analyze debe pasar sin violaciones antes de /speckit.implement.
