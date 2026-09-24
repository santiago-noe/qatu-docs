/speckit.constitution Crea la constitución de Qatu, un marketplace de alquiler de herramientas y servicios de oficios para Ayacucho (Perú) preparado para expandirse a otras ciudades. Lee primero docs/01-producto.md a docs/05-confianza-y-legal.md. Principios no negociables:

I. Especificación primero. Ninguna funcionalidad se implementa sin spec aprobado. Las reglas de negocio salen de docs/ o del spec; si faltan, se marcan [NEEDS CLARIFICATION], nunca se inventan.

II. Dominio en el centro. Cada módulo (identity, catalog, listings, providers, search, rentals, service-jobs, payments, deposits, messaging, notifications, reviews, disputes, admin) tiene capa domain sin dependencias de framework. Los módulos solo se comunican por su API pública o por eventos de dominio vía outbox. Monolito modular; nada de microservicios sin justificación medida.

III. Estados explícitos. Toda entidad transaccional (reserva, trabajo, cotización, garantía, pago, disputa) cambia de estado solo mediante su máquina de estados, con validación de actor y guardas, registro en audit_log y evento en outbox en la misma transacción de BD.

IV. Dinero correcto. Montos en céntimos enteros, moneda explícita (PEN). Ledger de doble partida inmutable. Snapshot inmutable del desglose de precio en cada transacción. Nunca comisión sobre garantías ni sobre reembolso de materiales. Todo endpoint que cree transacciones o pagos es idempotente. Qatu no custodia fondos de terceros: los pagos pasan por la interfaz PaymentGateway (adaptadores Manual, MercadoPagoSplit, Culqi).

V. Integridad bajo concurrencia. Disponibilidad protegida por restricción de exclusión en PostgreSQL (sin doble reserva). Bloqueo optimista con columna version. Jobs de timeout idempotentes.

VI. Test-first en el dominio. Tests unitarios obligatorios para máquinas de estado, cálculo de precios, comisiones y reglas de garantía; tests de integración contra Postgres real (Testcontainers) para cada caso de uso; e2e (Playwright) para los flujos críticos: reservar herramienta, contratar servicio, abrir disputa. Cobertura mínima de dominio 90%.

VII. Contratos primero. Esquemas Zod en packages/contracts compartidos entre web y api; OpenAPI generado y versionado (/v1). Cambios que rompen contrato requieren nueva versión.

VIII. Privacidad por diseño (Ley 29733 y D.S. 016-2024-JUS). Minimización de datos, consentimiento por finalidad, consentimiento expreso para biometría, archivos sensibles en bucket privado con URLs prefirmadas de corta duración, dirección exacta y teléfono visibles solo tras confirmar la transacción, accesos a datos sensibles auditados.

IX. Seguridad. RBAC + verificación de propiedad en cada caso de uso, rate limiting, OTP con límite de intentos, códigos de entrega hasheados, OWASP ASVS nivel 2, sin secretos en el repo.

X. Escalable por configuración. city_id en todas las entidades con ubicación; comisiones, timeouts y políticas en platform_settings por ciudad/categoría; feature flags para lanzamientos graduales; API stateless.

XI. Móvil y conexión lenta primero. Web PWA responsive, presupuesto de rendimiento (LCP < 2,5 s en 4G lento, JS inicial < 200 KB), imágenes WebP redimensionadas, UI en español claro (es-PE) y accesible (WCAG 2.1 AA).

XII. Transparencia al usuario. El total (alquiler/servicio + tarifa + delivery + garantía) se muestra desglosado antes de confirmar. La IA nunca inventa precios, disponibilidad ni condiciones y se identifica como IA.

XIII. Simplicidad. YAGNI; la dependencia nueva debe justificarse en research.md. Preferir Postgres (FTS, PostGIS, pgvector) antes que añadir otro motor.

Gobernanza: la constitución prevalece sobre specs y planes; enmiendas con versión semántica y fecha; /speckit.analyze debe pasar sin violaciones antes de /speckit.implement.
