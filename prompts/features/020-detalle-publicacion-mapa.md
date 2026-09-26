# 020 — Detalle de publicación con mapa

- **Fecha:** 2026-09-26
- **Estado:** borrador para `/speckit.specify`
- **Rama sugerida:** `020-detalle-publicacion-mapa` (en `qatu-api` y `qatu-app`)
- **Repos afectados:** `qatu-app` (ficha, mapa, tarjeta de reserva), `qatu-api` (endpoints públicos, ubicación ofuscada, caché), `qatu-docs` (este spec y colección Bruno)
- **Depende de:** 002 categorías y ubicación (zonas), 003 publicaciones de alquiler (datos de la ficha). Comparte el componente de mapa con 005 búsqueda (resultados en lista y mapa). La acción "Reservar" se conecta con 006.
- **Fuente de negocio:** docs/02 (flujo A2), docs/03 ("Ubicación y mapa"), docs/05 ("Ubicación y privacidad en el mapa")
- **Inspiración:** la ficha de un anuncio de Airbnb: al abrir un producto se ve todo el detalle y, muy visible, dónde se encuentra, sin revelar la dirección exacta.

## SPECIFY
```
/speckit.specify Cuando un cliente abre una herramienta publicada debe ver la ficha completa y, de forma prominente, dónde se encuentra, para decidir con confianza (flujo A2 en docs/02). Es la pantalla donde más se decide el alquiler.

Historias:
(P1) Como cliente quiero ver las fotos, el título, la categoría, la descripción, los accesorios incluidos y las reglas de uso de la herramienta.
(P1) Como cliente quiero ver un mapa "Dónde se encuentra" con una zona aproximada y el distrito o barrio, para saber si me queda cerca, sin que se muestre la dirección exacta.
(P1) Como cliente quiero ver la distancia aproximada desde mi ubicación o desde el distrito que elegí.
(P1) Como cliente quiero elegir fechas en un calendario donde los días no disponibles aparecen bloqueados, y ver el precio calculado para ese rango.
(P1) Como cliente quiero ver el desglose total antes de reservar: alquiler + tarifa de servicio + delivery (si aplica) + garantía, en soles.
(P1) Como cliente quiero ver la garantía, la política de cancelación y el nivel de verificación mínimo requerido.
(P1) Como cliente quiero ver la reputación y el estado de verificación del arrendador.
(P1) Como cliente quiero saber que la dirección exacta y el teléfono se comparten cuando se confirma la reserva.
(P1) Como visitante sin sesión quiero ver la ficha completa y ser llevado a iniciar sesión solo cuando quiero reservar.
(P2) Como cliente quiero ver otras herramientas cercanas o del mismo arrendador.
(P2) Como cliente quiero guardar la publicación en favoritos y compartir su enlace.
(P2) Como cliente quiero reportar una publicación sospechosa.
(P2) Como arrendador quiero ver una vista previa de cómo verán mi publicación y mi ubicación aproximada.

Reglas: el mapa público nunca muestra el punto exacto ni la dirección; la ubicación exacta y el teléfono solo se revelan tras confirmar la transacción y ese acceso queda registrado; las coordenadas públicas se calculan con desplazamiento determinista para que no puedan recuperarse consultando varias veces; la disponibilidad y el precio mostrados se recalculan y validan en el servidor al reservar; el precio de una reserva se guarda como snapshot inmutable; se muestra solo información real, sin reseñas ni datos inventados; funciona en móvil con conexión lenta (el mapa se carga de forma diferida); accesible (WCAG 2.1 AA) con alternativa textual al mapa; la publicación es indexable (SEO) sin exponer datos privados.
```

## Preguntas guía para /speckit.clarify
- ¿Radio de ofuscación por defecto (por ejemplo 300 a 500 m) y si varía por zona?
- ¿La zona se muestra como círculo, como polígono del distrito o como ambos?
- ¿Proveedor de tiles para producción? El servidor público de OSM no admite tráfico comercial.
- ¿La distancia se calcula desde la geolocalización del navegador o solo desde el distrito elegido?
- ¿Se permite ver la ficha sin sesión? (recomendado: sí, con acceso a reservar solo con sesión)
- ¿La tarjeta de reserva permite pedir la reserva desde esta feature o solo llega hasta el desglose (la reserva es la 006)?
- ¿Se muestran herramientas cercanas en esta feature o en 005?
- ¿Qué pasa con publicaciones pausadas o eliminadas al abrir su enlace (404, aviso, alternativas)?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
ARQUITECTURA

qatu-api (Go):
- Dominio: value object PublicLocation en internal/core/domain con la regla de ofuscación (punto exacto + radio + semilla por publicación → punto público determinista dentro del radio); el punto exacto nunca aparece en el modelo de lectura pública.
- Ports: ListingReadRepository (ficha pública), AvailabilityReader, PriceQuoter, ListingCache. Services: ListingDetailService (ensambla la ficha), QuoteService (calcula precio para un rango, sin persistir).
- Adaptadores: postgres/listing_read_repository.go (consulta con PostGIS; ST_DWithin para cercanas con índice GIST), redisclient/listing_cache.go (cache-aside), handler/listing_handler.go.
- Rutas públicas (sin sesión): GET /api/v1/listings/{id} · GET /api/v1/listings/{id}/availability?from&to · GET /api/v1/listings/{id}/quote?from&to&fulfillment · GET /api/v1/listings/{id}/nearby.
- Ruta autenticada con verificación de propiedad y de transacción confirmada: GET /api/v1/bookings/{id}/location (dirección exacta y teléfono), con registro en audit_log. La reserva en sí pertenece a la feature 006.
- Redis: ficha pública en cache-aside con TTL corto (60 s) con jitter e invalidación al editar la publicación; singleflight para evitar estampida. Disponibilidad y cotización NO se cachean como verdad (se calculan contra Postgres; a lo sumo una caché de 5 s para absorber picos, nunca al reservar).
- Migraciones: agregar columnas public_location (geography), public_radius_m y zone_id a listings si no existen, con índice GIST sobre public_location; migración reversible. La semilla de ofuscación se deriva de un secreto de servidor y el id de la publicación (no se guarda en claro).
- Rate limiting en Redis por IP sobre estos endpoints públicos; respuestas con Cache-Control adecuado para la ficha.

qatu-app (Next.js 16):
- Ruta: app/(public)/herramientas/[id]/page.tsx renderizada en servidor (SSR con revalidación) para SEO; metadatos y JSON-LD sin datos privados; app/not-found para publicaciones inexistentes.
- Features: features/public/listing-detail/{components,lib}: gallery, listing-summary, availability-calendar, price-breakdown, booking-card (fija en escritorio y barra inferior en móvil), host-card (reputación y verificación), location-section, nearby-listings, report-button.
- Mapa: componente compartido con 005 en components/map/ (nuevo directorio compartido; justificar en research.md). MapLibre GL JS con importación dinámica (next/dynamic, ssr:false) que carga solo al acercarse a la sección "Dónde se encuentra" (IntersectionObserver), con un placeholder estático y el nombre del distrito mientras carga. Dibuja el círculo o la zona, nunca un pin exacto.
- El navegador solo llama al BFF: app/api/listings/[id]/... reenvía a qatu-api; los datos de la ficha se obtienen en el servidor con lib/api.ts.
- Cálculo de precio: la UI pide /quote al cambiar las fechas; el total mostrado es informativo y se revalida en el servidor al reservar.
- Sin sesión: la tarjeta de reserva lleva a /auth/signin con retorno a la ficha; proxy.ts no bloquea esta ruta pública.
- Rendimiento: JS inicial < 200 KB (el mapa queda fuera del bundle inicial); imágenes next/image con tamaños responsivos; LCP < 2,5 s en 4G lento.

CONTRATOS
- GET /api/v1/listings/{id} -> 200 {id, title, category, description, photos[], accessories[], rules, cancelPolicy, minVerification, deposit, host{name, verificationLevel, rating?}, location{zone, district, publicCenter{lat,lng}, radiusM}} | 404. Nunca incluye la ubicación exacta ni el teléfono.
- GET /api/v1/listings/{id}/availability?from&to -> 200 {blockedRanges[]}
- GET /api/v1/listings/{id}/quote?from&to&fulfillment -> 200 {rental, serviceFee, delivery, deposit, total, currency:"PEN"} (céntimos enteros) | 409 no disponible
- GET /api/v1/bookings/{id}/location -> 200 {address, exactPoint, phone} solo con reserva confirmada | 403
Documentar en la colección Bruno de qatu-docs (bruno/Listings/).

PARÁMETROS (platform_settings, por ciudad): radio de ofuscación por defecto, TTL de la caché de la ficha, distancia máxima de "cercanas", cantidad de resultados cercanos.

PRUEBAS
- Dominio (go test, tabla de casos): la ofuscación es determinista (misma publicación → mismo punto público), el punto público queda dentro del radio, y el punto exacto no puede recuperarse promediando consultas; cálculo de precio por combinación de tarifas (hora, día, fin de semana, semana, mes); el desglose suma el total y la garantía no lleva comisión.
- Privacidad: prueba automática que recorre la respuesta pública de la ficha y falla si aparece el punto exacto, la dirección o el teléfono; el endpoint de ubicación exacta responde 403 sin reserva confirmada y registra el acceso en audit_log.
- Integración (Testcontainers con Postgres + PostGIS y Redis): ficha pública, disponibilidad con bloqueos solapados, cotización, "cercanas" con ST_DWithin, invalidación de la caché al editar la publicación, rate limiting.
- Concurrencia: dos cotizaciones simultáneas no afectan la disponibilidad; la disponibilidad nunca se sirve desde una caché al reservar.
- Frontend (bun test): lib de formato de moneda en soles y de rango de fechas, cálculo de distancia, componentes de calendario y desglose.
- E2E (Playwright, viewports 360x640 y 1280x800): abrir la ficha sin sesión, ver el mapa con el círculo (nunca un pin exacto), elegir fechas y ver el precio, fechas no disponibles bloqueadas, tarjeta de reserva fija en escritorio y barra inferior en móvil, ir a iniciar sesión al reservar, publicación inexistente muestra 404.
- Accesibilidad: axe sin violaciones serias; el mapa tiene alternativa textual (distrito y distancia); calendario operable con teclado; foco visible.
- Rendimiento: Lighthouse CI en móvil (Performance ≥ 90, LCP < 2,5 s, JS inicial < 200 KB); verificar que el bundle de MapLibre no entra en la carga inicial.
- SEO: metadatos, Open Graph y JSON-LD válidos, sin datos privados.

DEFINICIÓN DE TERMINADO
Tests y lint en verde en ambos repos, umbrales de Lighthouse cumplidos, prueba de privacidad pasando, migración reversible, OpenAPI y colección Bruno actualizados, .env.example actualizado, y la sección "Estado de implementación" de los requerimientos en qatu-docs al día. Dependencias nuevas (MapLibre GL JS y proveedor de tiles) justificadas en research.md.
```

## Guía para /speckit.tasks
Orden sugerido: 1) dominio (PublicLocation, cotización) con sus pruebas, 2) migraciones y repositorio de lectura, 3) endpoints públicos y caché, 4) prueba de privacidad automática, 5) endpoint de ubicación exacta con audit_log, 6) BFF, 7) componentes de la ficha (galería, resumen, calendario, desglose), 8) mapa diferido y sección "Dónde se encuentra", 9) tarjeta de reserva y estado sin sesión, 10) SEO, 11) pruebas e2e, accesibilidad y Lighthouse. Marcar `[P]` las tareas independientes entre `qatu-api` y `qatu-app` una vez fijado el contrato.
